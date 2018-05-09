# b-mail dapp test browser local keys store

Test example to test the storage of private keys on the browser using eos js libs

1 - Compile eos.js lib explained here: https://github.com/EOSIO/eosjs#browser

2 - Compile eosjs-keygen.js lib explained here: https://github.com/EOSIO/eosjs-keygen#browser

3 - Run html page bellow with eos.js and eosjs-keygen.js in the same folder

4 - Click in Test button and check console log


```html
<html>
<head>
  <meta charset="utf-8">
  <script src="eos.js"></script>
  <script src="eosjs-keygen.js"></script>
</head>
<body>
<div>
  <h2>b-mail dapp test browser local keys store</h2>
	<button onclick="testStoreKeys();" >Test store private key</button> Check console...
</div>

<script>
//system account keys
var eosioPrivate = "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3";
var eosioPublic = "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV";

function testStoreKeys() 
{ 
  //TODO: get private key from login box and generate publickey from private? For now using eosio system keys
  //      get account name form login box too
  let privateKey = eosioPrivate;
  let publickey = eosioPublic;
  let accountname = "accountTest1";

  sessionConfig = {
    timeoutInMin: 30,
    uriRules: {   //TODO: review permissions/rules
      'active': '.*',
      'active/**': '.*'
    },
  }

  let mykeystore = kos.Keystore(accountname, sessionConfig);

  //save private key local in browser
  //TODO: review permissions, get accountPermissions from eos get account api
  mykeystore.deriveKeys(
    { 
      parent: privateKey ,
      accountPermissions: [{
        perm_name: 'active',
        parent: 'owner',
        required_auth: {
        threshold: 1,
        keys: [{
            key: publickey,
            weight: 1
          }
        ],
        accounts: []
        }
      }]
    }
  );

  console.log("created mykeystore");
  
  var returedPrivateKey = mykeystore.getPrivateKey('active');

  console.log("returned private key: " + returedPrivateKey);
  console.log("check browser local storage");

  //TODO: next steps:
  /*
    var config = {
      keyProvider: mykeystore.keyProvider,
      httpEndpoint: "http://nodeos01.btuga.io",
      expireInSeconds: 60,
      broadcast: true,
      debug: false,
      sign: true
    };

    var eos = Eos.Localnet(config);

    //encrypt
    var encrypted = Eos.modules.ecc.Aes.encrypt(returedPrivateKey, publicWifBob, text);

    //create transaction
     eos.transaction({...
  */
}
</script>
</body>
</html>
```
