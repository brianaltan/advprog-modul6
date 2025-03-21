### Reflection: Commit 1

```
use std::{ 
    io::{prelude::*, BufReader}, 
    net::{TcpListener, TcpStream}, 
}; 

fn main() { 
    let listener = TcpListener::bind("127.0.0.1:7878").unwrap(); 

    for stream in listener.incoming() { 
        let stream = stream.unwrap(); 
        handle_connection(stream);  // Calls a function to handle the request
    } 
} 
```

At first, the server was only programmed to listen for incoming connections. However, after modifying the code with `handle_connection`, the server is now able to read and parse incoming HTTP requests through `BufReader` to read request lines from the browser.