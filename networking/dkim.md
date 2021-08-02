# DKIM

- Domain Keys Identified Mail
- [DKIM: What is it and why is it important?](https://postmarkapp.com/guides/dkim#:~:text=DKIM%20(DomainKeys%20Identified%20Mail)%20is,it%20leaves%20a%20sending%20server.)
- security standard designed to make sure messages aren't altered in transit between the sending and recipient servers
- uses public key cryptography to sign email with a private key as it leaves a sending server
- optional security protocol and not a universally adopted standard
- ISPs use DKIM to build a reputation on your domain over time
  - having a good reputation improves deliverability
- does not encrypt message content
