# edu-https
[s_client](https://docs.openssl.org/1.0.2/man1/s_client/)  
[OpenSSL](https://docs.openssl.org/master/)
[tshark](https://www.wireshark.org/docs/man-pages/tshark.html)
[tcpdump](https://www.tcpdump.org)
[Wireshark](https://www.wireshark.org)

## Instructions

```bash
openssl s_client -connect localhost:443
```

## Get certificate from local host

```bash
echo | openssl s_client -connect localhost:443 -servername localhost 2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > localhost.pem

echo | openssl s_client -connect jensenyh.se:443  2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > jensen.pem
```

## openssl

### Inspect

```bash
openssl x509 -in localhost.pem -text -noout
```

### Get public key

```bash
openssl x509 -in localhost.pem -pubkey -noout
```

### Verify cert

```bash
openssl verify -CAfile localhost.pem localhost.pem
```

### Monitoring

```bash
sudo tshark -i lo0 -f "port 80 or port 443"
sudo tcpdump -i lo0 "port 80 or port 443"
sudo tcpdump -i lo0 -s 0 -A "port 80 or port 443"

```

### Testing post

```bash
curl -d "username=alice&password=secret123" -X POST http://localhost
curl -d "username=alice&password=secret123" -X POST https://localhost
```
