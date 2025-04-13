Creating an SSL certificate from scratch in C++ using the OpenSSL library involves several steps. Below, I will guide you through creating a simple program that generates a self-signed SSL certificate. This example will consist of a header file and a source file.

### Step 1: Install OpenSSL

Before you start, make sure you have OpenSSL installed on your system. You can fetch the libraries from the [OpenSSL website](https://www.openssl.org/source/) or use your system's package manager.

On Ubuntu, you can install it as follows:

```bash
sudo apt-get install libssl-dev
```

On macOS, use Homebrew:

```bash
brew install openssl
```

### Step 2: Create the Header File

Create a header file named `SSLCertificateGenerator.h`. This header file will declare the class responsible for generating the SSL certificate.

```cpp
// SSLCertificateGenerator.h

#ifndef SSL_CERTIFICATE_GENERATOR_H
#define SSL_CERTIFICATE_GENERATOR_H

#include <openssl/pem.h>
#include <openssl/x509.h>
#include <openssl/rsa.h>

class SSLCertificateGenerator {
public:
    SSLCertificateGenerator();
    ~SSLCertificateGenerator();
    
    bool generateCertificate(const std::string& certFile, const std::string& keyFile);

private:
    RSA* generateRSAKey(int keyLength);
    X509* createSelfSignedCert(RSA* rsaKey);
};

#endif // SSL_CERTIFICATE_GENERATOR_H
```

### Step 3: Create the Source File

Create a source file named `SSLCertificateGenerator.cpp`, where you will implement the functionality declared in your header file.

```cpp
// SSLCertificateGenerator.cpp

#include "SSLCertificateGenerator.h"
#include <openssl/x509_vfy.h>
#include <openssl/err.h>
#include <ctime>

SSLCertificateGenerator::SSLCertificateGenerator() {}

SSLCertificateGenerator::~SSLCertificateGenerator() {}

RSA* SSLCertificateGenerator::generateRSAKey(int keyLength) {
    RSA* rsa = RSA_generate_key(keyLength, RSA_F4, NULL, NULL);
    return rsa;
}

X509* SSLCertificateGenerator::createSelfSignedCert(RSA* rsaKey) {
    X509* x509 = X509_new();
    
    // Set version to X509v3
    X509_set_version(x509, 2);
    
    // Set serial number
    ASN1_INTEGER_set(X509_get_serialNumber(x509), 1);
    
    // Set the validity period
    X509_gmtime_adj(X509_get_notBefore(x509), 0);
    X509_gmtime_adj(X509_get_notAfter(x509), 31536000L); // 1 year validity
    
    // Set subject name
    X509_NAME* name = X509_NAME_new();
    X509_NAME_add_entry_by_txt(name, "C",  MBSTRING_ASC, (unsigned char *)"US", -1, -1, 0);
    X509_NAME_add_entry_by_txt(name, "CN", MBSTRING_ASC, (unsigned char *)"My Self-Signed Certificate", -1, -1, 0);
    X509_set_subject_name(x509, name);
    X509_set_issuer_name(x509, name);
    
    // Set public key
    EVP_PKEY* pkey = EVP_PKEY_new();
    EVP_PKEY_assign_RSA(pkey, rsaKey);
    X509_set_pubkey(x509, pkey);
    
    // Sign the certificate
    X509_sign(x509, pkey, EVP_sha256());
    
    EVP_PKEY_free(pkey);
    X509_NAME_free(name);
    
    return x509;
}

bool SSLCertificateGenerator::generateCertificate(const std::string& certFile, const std::string& keyFile) {
    RSA* rsaKey = generateRSAKey(2048);
    if (!rsaKey) {
        return false;
    }
    
    X509* x509 = createSelfSignedCert(rsaKey);
    if (!x509) {
        RSA_free(rsaKey);
        return false;
    }
    
    // Write the private key to a file
    FILE* keyOut = fopen(keyFile.c_str(), "wb");
    if (keyOut) {
        PEM_write_RSAPrivateKey(keyOut, rsaKey, NULL, NULL, 0, NULL, NULL);
        fclose(keyOut);
    }
    
    // Write the certificate to a file
    FILE* certOut = fopen(certFile.c_str(), "wb");
    if (certOut) {
        PEM_write_X509(certOut, x509);
        fclose(certOut);
    }
    
    // Cleanup
    X509_free(x509);
    RSA_free(rsaKey);
    
    return true;
}
```

### Step 4: Create the Main Program

Create a `.cpp` file named `main.cpp` to test the `SSLCertificateGenerator`.

```cpp
// main.cpp

#include <iostream>
#include "SSLCertificateGenerator.h"

int main() {
    SSLCertificateGenerator certGen;

    if (certGen.generateCertificate("self_signed_cert.pem", "private_key.pem")) {
        std::cout << "Self-signed SSL certificate generated successfully!" << std::endl;
    } else {
        std::cout << "Failed to generate SSL certificate." << std::endl;
    }

    return 0;
}
```

### Step 5: Compile the Program

To compile your program, link it against the OpenSSL libraries. Here is how you can do that:

```bash
g++ -o ssl_cert_generator main.cpp SSLCertificateGenerator.cpp -lssl -lcrypto
```

### Step 6: Run the Program

Execute your program to create the self-signed certificate and private key:

```bash
./ssl_cert_generator
```

### Summary

Now you have a simple C++ program that creates a self-signed SSL certificate using OpenSSL. It generates a private key and stores both the key and the certificate in PEM format files. You can extend the functionality and error handling as needed for your specific requirements.
