# Create Server and Client Certificates and Keys

Run the below commands from your workstation where you have AWS CLI installed (for Linux).

1. Clone the easy-rsa repo  
   ```bash
   $ git clone https://github.com/OpenVPN/easy-rsa.git  
   $ cd easy-rsa/easyrsa3  
2. Initialize PKI environment
   ````bash
    $ ./easyrsa init-pki  
3. Create new Certification Authority (CA)
   ```bash
    $./easyrsa build-ca nopass 
