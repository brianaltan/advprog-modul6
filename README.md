### Reflection: Commit 1

```rust
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

### Reflection: Commit 2
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let http_request: Vec<_> = buf_reader
        .lines()
        .map(|result| result.unwrap())
        .take_while(|line| !line.is_empty())
        .collect();
    let status_line = "HTTP/1.1 200 OK";
    let contents = fs::read_to_string("hello.html").unwrap();
    let length = contents.len();
    let response = format!("{status_line}\r\nContent-Length:
   {length}\r\n\r\n{contents}");
    stream.write_all(response.as_bytes()).unwrap();
}
```

After modifying the code, the Rust compiler provided a warning about an unused variable, `http_request`. This warning indicates that the variable has been declared but never used. It recommends prefixing the variable with an underscore to suppress the warning, acknowledging its presence while indicating that there is no plan to use it.

![Commit 2 screen capture](/assets/images/commit2.png)


### Reflection: Commit 3
```rust
let (status_line, filename) = if request_line.starts_with("GET / ") {
    ("HTTP/1.1 200 OK", "hello.html")
} else {
    ("HTTP/1.1 404 NOT FOUND", "error.html")
};
```

At first, all responses were served with `hello.html` regardless of the requested path. To complete the third milestone, I created another file called `error.html`. When the HTTP status line returns "Sorry, I don't know what you're asking for.", the server responds with `error.html`, and when it returns 200 OK, it serves `hello.html`.  

![Commit 3 screen capture](/assets/images/commit3.png)

### Reflection: Commit 4
```rust
"GET /sleep HTTP/1.1" => {
    thread::sleep(Duration::from_secs(10));
    ("HTTP/1.1 200 OK", "hello.html")
}
```

After adding `/sleep`, we have to wait if a request is made to the sleep route until it is completed before serving other website requests made afterward. This happens because the program is single-threaded.