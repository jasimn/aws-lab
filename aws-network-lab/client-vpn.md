# Create Server and Client Certificates and Keys

Run the below commands from your workstation where you have AWS CLI installed (for Linux).

1. Clone the easy-rsa repo  
   ```bash
     git clone https://github.com/OpenVPN/easy-rsa.git
2. go to easy-rsa directory
   ```bash
     cd easy-rsa/easyrsa3  
3. Initialize PKI environment
   ````bash
      ./easyrsa init-pki  
4. Create new Certification Authority (CA)
   ```bash
     ./easyrsa build-ca nopass 
5.Generate the server certificate and key
   ```bash
    ./easyrsa build-server-full server nopassmarkdown
# Create Server and Client Certificates and Keys

6.Generate the client certificate and key
   ```bash
    ./easyrsa build-client-full client1.domain.idi nopass  
7.Copy server and client certificates and keys to one directory
```bash
  mkdir ~/demo  
  cp pki/ca.crt ~/demo/  
  cp pki/issued/server.crt ~/demo/  
  cp pki/private/server.key ~/demo/  
  cp pki/issued/client1.domain.idi.crt ~/demo/  
  cp pki/private/client1.domain.idi.key ~/demo/  
  cd ~/demo  
