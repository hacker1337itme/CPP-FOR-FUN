To improve the C++ HTTP client example I provided earlier and to follow better coding practices, it's a good idea to separate the class declaration and implementation into a header file (`HttpClient.h`) and a source file (`HttpClient.cpp`). Below is the revised implementation including the header file.

### HttpClient.h
This header file contains the class declaration.

```cpp
#ifndef HTTP_CLIENT_H
#define HTTP_CLIENT_H

#include <string>

class HttpClient {
public:
    HttpClient(const std::string &host, int port);
    void get(const std::string &path);
    void post(const std::string &path, const std::string &data);

private:
    std::string host;
    int port;
    int sock;

    void connectSocket();
    void sendRequest(const std::string &method, const std::string &path, const std::string &data = "");
    void receiveResponse();
    void closeSocket();
};

#endif // HTTP_CLIENT_H
```

### HttpClient.cpp
This source file contains the implementation of the class declared in the header file.

```cpp
#include "HttpClient.h"
#include <iostream>
#include <cstring>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>

HttpClient::HttpClient(const std::string &host, int port) : host(host), port(port) {}

void HttpClient::get(const std::string &path) {
    connectSocket();
    sendRequest("GET", path);
    receiveResponse();
    closeSocket();
}

void HttpClient::post(const std::string &path, const std::string &data) {
    connectSocket();
    sendRequest("POST", path, data);
    receiveResponse();
    closeSocket();
}

void HttpClient::connectSocket() {
    sock = socket(AF_INET, SOCK_STREAM, 0);
    if (sock < 0) {
        perror("socket");
        exit(EXIT_FAILURE);
    }

    struct sockaddr_in server_address{};
    server_address.sin_family = AF_INET;
    server_address.sin_port = htons(port);
    inet_pton(AF_INET, host.c_str(), &server_address.sin_addr);

    if (connect(sock, (struct sockaddr *)&server_address, sizeof(server_address)) < 0) {
        perror("connect");
        exit(EXIT_FAILURE);
    }
}

void HttpClient::sendRequest(const std::string &method, const std::string &path, const std::string &data) {
    std::string request;
    if (method == "GET") {
        request = method + " " + path + " HTTP/1.1\r\n";
    } else if (method == "POST") {
        request = method + " " + path + " HTTP/1.1\r\n";
        request += "Content-Length: " + std::to_string(data.size()) + "\r\n";
        request += "Content-Type: application/x-www-form-urlencoded\r\n";
    }
    request += "Host: " + host + "\r\n";
    request += "Connection: close\r\n\r\n";
    if (!data.empty()) {
        request += data;
    }

    send(sock, request.c_str(), request.size(), 0);
}

void HttpClient::receiveResponse() {
    char buffer[4096];
    int received;
    while ((received = recv(sock, buffer, sizeof(buffer) - 1, 0)) > 0) {
        buffer[received] = '\0';
        std::cout << buffer;
    }
}

void HttpClient::closeSocket() {
    close(sock);
}
```

### main.cpp
Finally, make the main program to utilize the `HttpClient`.

```cpp
#include "HttpClient.h"

int main() {
    HttpClient client("httpbin.org", 80);

    // Example of a GET request
    std::cout << "GET request:\n";
    client.get("/get");

    // Example of a POST request
    std::cout << "\nPOST request:\n";
    client.post("/post", "name=value&foo=bar");

    return 0;
}
```

### Compilation
To compile the above implementation, you can use the following command which assumes you have three separate files (`HttpClient.h`, `HttpClient.cpp`, and `main.cpp`):

```bash
g++ -o http_client main.cpp HttpClient.cpp
```

### Running the Program
After compiling, you can run the program using:

```bash
./http_client
```

### Key Points:
- **Header Files**: Help to separate the interface (declarations) from the implementation, promoting better organization and reusability.
- **Namespaces**: Consider placing your class within a namespace as your project grows, to avoid naming collisions.
- **Error Handling**: This example uses simple error handling; in real applications, consider using exceptions or other mechanisms for better error handling.
- **Extensibility**: You can extend this basic client by adding more HTTP methods, headers, or even handling HTTPS using libraries like OpenSSL.

