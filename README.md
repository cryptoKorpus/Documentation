# Documentation

b-mail smartcontract model

TESTE

Permite emails privados ou publicos com attachments privados ou publicos.

inputs para o smart contract:

```
{
  "src_account": "rmumbray0@histats.com",
  "dest_account": "dmcpheat0@wordpress.com",
  "date": "2018-03-15T05:00Z",
  "isEncripted": true,
  "payload": "a1bfa98cda2b42fe2b7ad9ba8b825dc680a781ee" // Hash to IPFS content
}
```

payload pode ter o seguinte json:
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
Numa versão V2

Pode-se adicionar no payload do email ou fora.
```
"cc_accounts": ["rmrasdasay0@histaasdts.com", "xpto@active.com"],
"bcc_accounts": ["asdasgo@test.net", "pogaew@google.blockchain"],
```

No EOS 1 transacao pode receber N mensagens.
Tem de se ver como se obtem os dados do smart contract
