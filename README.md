# miniServ - Lightweight HTTP Web Server

A custom-built HTTP/1.1 web server written in C++, featuring event-driven architecture with epoll, configurable server instances, dynamic routing, and CGI support for PHP and Python scripts.

## Features

- **Multi-server support**: Run multiple independent servers on different ports/IPs from a single configuration file
- **Event-driven architecture**: Uses epoll for efficient socket polling and handling concurrent connections
- **Dynamic routing**: Route-based configuration with location blocks and regex pattern matching
- **CGI execution**: Execute PHP and Python scripts as server-side applications
- **HTTP methods**: Support for GET, POST, DELETE
- **File uploads**: Configurable upload directories and automatic content handling
- **Custom error pages**: Define custom HTML responses for specific HTTP status codes
- **Session management**: Client session tracking and connection handling
- **Buffered I/O**: Efficient request/response buffering for large file transfers
- **Configuration parser**: NGINX-like configuration syntax for easy setup

## Building

Build the project using the provided Makefile:

```bash
make          # Build the webserv binary
make clean    # Remove object files
make fclean   # Remove all compiled files
make re       # Rebuild from scratch
```

Run unit tests:

```bash
make utest    # Compile and run unit tests
```

## Usage

### Basic Run

```bash
./webserv <config_file>
```

Example with included configuration:

```bash
./webserv confs/basic.conf
```

### Configuration Format

The server uses an NGINX-like configuration syntax. Here's a basic example:

```properties
server {
	listen						127.0.0.1:8080;
	server_name					localhost;
	root						pages;
	index						index.html index.php;
	client_max_body_size		1000000;
	allowed_methods				GET;

	location /upload {
		allowed_methods			GET POST DELETE;
		upload					pages/upload;
		root					pages/upload;
	}

	location ~ api/* {
		root					pages/api;
	}

	error_page 404 /404.html;
	error_page 500 /500.html;
}
```

### Configuration Options

- **listen**: IP address and port for the server to bind to
- **server_name**: Server name for virtual hosting
- **root**: Base directory for serving files
- **index**: Default files to serve when accessing a directory
- **client_max_body_size**: Maximum allowed request body size in bytes
- **allowed_methods**: HTTP methods allowed at this level (GET, POST, DELETE)
- **location**: Route block with path or regex pattern
- **upload**: Directory where uploaded files are stored
- **error_page**: Custom error page mapping (e.g., `error_page 404 /404.html`)

## Project Structure

```
miniServ/
├── src/               # Source code
│   ├── main.cpp       # Entry point
│   ├── buffer/        # Request/response buffering
│   ├── config/        # Configuration parsing
│   ├── execution/     # CGI script execution (PHP, Python)
│   ├── filling/       # HTTP content handling (GET, POST, DELETE)
│   ├── parsing/       # Request parsing and config file parsing
│   ├── polling/       # Socket and event polling (epoll)
│   ├── request/       # HTTP request handling
│   ├── response/      # HTTP response generation
│   ├── routing/       # Route matching and dispatching
│   └── signal/        # Signal handling
├── inc/               # Header files
├── pages/             # Default web content and test files
├── confs/             # Example configuration files
├── tests/             # HTTP test files and unit tests
└── obj/               # Compiled object files (generated)
```

## Example Configurations

Several pre-configured examples are included:

- **basic.conf**: Simple single-server setup
- **routes.conf**: Server with multiple location routes
- **2ServOnDifferentPort.conf**: Two servers on different ports
- **2ServOnSamePort.conf**: Two server on same port (should fail)
- **autoindex.conf**: Directory listing example
- **customError.conf**: Custom error page handling

## HTTP Status Codes Supported

### 2xx Success
- 200 OK
- 201 Created

### 4xx Client Errors
- 400 Bad Request
- 403 Forbidden
- 404 Not Found
- 405 Method Not Allowed
- 408 Request Timeout
- 411 Length Required
- 413 Payload Too Large
- 414 URI Too Long
- 415 Unsupported Media Type

### 5xx Server Errors
- 500 Internal Server Error
- 503 Service Unavailable
- 505 HTTP Version Not Supported
- 507 Insufficient Storage

## Testing

The project includes HTTP test files for manual testing:

- **tests/correct.http**: Valid HTTP requests
- **tests/incorrect.http**: Invalid/malformed requests

And comprehensive unit tests using the doctest framework in `tests/unittest/`.

## Requirements

- C++98 or later
- Linux (uses epoll system call)
