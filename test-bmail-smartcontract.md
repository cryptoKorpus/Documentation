Test example to test simple iteration with eosjs with bmail smart contract

<html>
<head>
<script src="eos.js"></script>
<script src="phaser.min.js"></script>
</head>
<body>
<div>
    <h2>Tic tac toe dapp</h2>
</div>
<div id="tictactoe"></div>
<script>

// system account keys
eosioPrivate = '5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3';
eosioPublic = 'EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV';

// Optional configuration..
config = {
  keyProvider: [eosioPrivate], // WIF string or array of keys..
  httpEndpoint: 'http://nodeos01.btuga.io',
  expireInSeconds: 60,
  broadcast: true,
  debug: false,
  sign: true
};

var eos = Eos.Localnet(config);

//create new account mynewacct
eos.newaccount({
  creator: 'eosio',
  name: 'mynewacct',
  owner: eosioPublic,
  active: eosioPublic,
  recovery: 'eosio'
})

//send email from mynewacct to eosio
eos.transaction({
  actions: [
    {
      account: 'bmail.code',
      name: 'sendmail',
      authorization: [{
        actor: 'mynewacct',
        permission: 'active'
      }],
      data: {
        sender: 'mynewacct',
        receivers: ['eosio'],
        mailhashs: ['QWERTYUIOPASDFGHJKLZXCVBNMDFGHJKLA']
      }
    }
  ]
})

</script>
</body>
</html>
