# Simple HTTP Server

This is a simple HTTP server implemented in C++ that handles basic GET and POST requests. It supports gzip compression for responses and can serve files from a specified directory.

## Features

- **GET /echo/{text}**: Echoes the text back to the client, with optional gzip compression.
- **GET /user-agent**: Returns the `User-Agent` header value from the client's request.
- **GET /files/{filename}**: Serves the specified file from the server's directory.
- **POST /files/{filename}**: Saves the request body as a file with the specified filename in the server's directory.
- Supports gzip compression for responses if the client accepts it.

## Requirements

- zlib library
- C++11 or later

## Building

1. Ensure you have `zlib` installed on your system.
2. Compile the server using a C++ compiler.

```sh
g++ -std=c++11 -lz -o http_server http_server.cpp
```

## Usage

Run the server with the `--directory` option to specify the directory where files will be served from and saved.

```sh
./http_server --directory /path/to/directory
```

The server listens on port `4221` and logs will appear on the console.

## Code Overview

### Gzip Compression

The function `compress_string` compresses a given string using gzip compression.

```cpp
std::string compress_string(const std::string& str, int compressionlevel = Z_BEST_COMPRESSION) {
    z_stream_s zs;
    memset(&zs, 0, sizeof(zs));
    if (deflateInit2(&zs, compressionlevel, Z_DEFLATED, 31, 8, Z_DEFAULT_STRATEGY) != Z_OK)
        throw(std::runtime_error("deflateInit failed while compressing."));
    zs.next_in = (Bytef*)str.data();
    zs.avail_in = str.size();
    int ret;
    char outbuffer[32768];
    std::string outstring;
    do {
        zs.next_out = reinterpret_cast<Bytef*>(outbuffer);
        zs.avail_out = sizeof(outbuffer);
        ret = deflate(&zs, Z_FINISH);
        if (outstring.size() < zs.total_out) {
            outstring.append(outbuffer, zs.total_out - outstring.size());
        }
    } while (ret == Z_OK);
    deflateEnd(&zs);
    if (ret != Z_STREAM_END) {
        throw(std::runtime_error("Exception during zlib compression: " + std::to_string(ret)));
    }
    return outstring;
}
```

### Client Handling

The function `handleClient` processes client requests.

```cpp
void handleClient(int client, std::string dir) {
    // Handling client requests and sending appropriate responses
}
```

### Main Function

The main function initializes the server, binds to a port, and listens for incoming connections.

```cpp
int main(int argc, char **argv) {
    // Server initialization and connection handling
}
```

## Example Requests

### GET /echo/{text}

```
GET /echo/hello HTTP/1.1
Host: localhost:4221
Accept-Encoding: gzip
```

### GET /user-agent

```
GET /user-agent HTTP/1.1
Host: localhost:4221
User-Agent: MyCustomClient/1.0
```

### GET /files/{filename}

```
GET /files/example.txt HTTP/1.1
Host: localhost:4221
```

### POST /files/{filename}

```
POST /files/newfile.txt HTTP/1.1
Host: localhost:4221
Content-Length: 11

Hello World
```

This simple HTTP server can be extended and modified to handle more complex use cases and protocols as needed.
