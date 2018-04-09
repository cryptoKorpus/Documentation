# Documentation

b-mail smartcontract model

Enable mail system that allows sending private mails using EOS smart contract
 
inputs for smart contract:

```
{
  "src_account": "rmumbray0@histats.com",
  "dest_account": "dmcpheat0@wordpress.com",
  "date": "2018-03-15T05:00Z",
  "isEncripted": true,
  "payload": "a1bfa98cda2b42fe2b7ad9ba8b825dc680a781ee" // Hash to IPFS content
}
```

payload can have the following json:
```
{
 "attachments":
  [{
    "isEncripted": true,
    "payload": "a1bfa9asdasda8cda2b42fe2asfqqb7ad9bae" // Hash to IPFS content
   },
   {
    "isEncripted": false,
    "payload": "a1bfa9asdasda8dhfsehscda2b4atb7ad9bas" // Hash to IPFS content
   }],
   "subject": "batatas da horta",
   "body": "As batatas da horta já estão prontas para colher",
   "tags": ["horta", "cultura"]
}
```
For V2 version

Can add on the mail payload or outside

```
"cc_accounts": ["rmrasdasay0@histaasdts.com", "xpto@active.com"],
"bcc_accounts": ["asdasgo@test.net", "pogaew@google.blockchain"],
```

In EOS 1 transaction can receive N messages.
Need to check how to get data from the smart contract, multindex?
