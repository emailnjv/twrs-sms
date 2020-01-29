# twrs_sms 

Twilio Rust: a very simple Twilio SMS API library written in Rust 

Example: 

```rust
use std::env::var;

use reqwest::StatusCode;
use twrs_sms;

fn main() -> Result<(), twrs_sms::TWRSError> {
        let tw_to = var("TW_TO").expect("Error getting $TW_TO from the environment");
        let tw_from = var("TW_FROM").expect("Error getting $TW_FROM from the environment");
        let tw_sid = var("TW_SID").expect("Error getting $TW_SID from the environment");
        let tw_token = var("TW_TOKEN").expect("Error getting $TW_TOKEN from the environment"); 

        // Create the request body and encode the message for the API
        let t: twrs_sms::TwilioSend = twrs_sms::TwilioSend{To: &tw_to, From: &tw_from, Body: "Hiya"};
        let t_s = t.encode().expect("Error converting to url encoded string");

        // Send the message to the API endpoint 
        let mut response = twrs_sms::send_message(&tw_sid, &tw_token, t_s)
            .expect("Error with HTTP request");
        
        // Server responds with 201 (Created) on the initial response
        assert_eq!(StatusCode::from_u16(201).unwrap(), response.status());

        // Run the loop to make sure the message was delivered
        let delivered = twrs_sms::is_delivered(&mut response, &tw_sid, &tw_token).expect("Error SMS not delivered");
        
        // Checking the delivered state, and fail on an error
        assert_eq!(delivered, "delivered");

        Ok(())
}
```

