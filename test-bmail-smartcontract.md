# Simple bmail dapp

Test example to test simple iteration with eosjs with bmail smart contract

1 - Compile eosjs lib explained here: https://github.com/EOSIO/eosjs#browser

2 - Change httpEndpoint to a network with a bmail smartcontract deployed by a bmail.code account

3 - Insert sender and receiver and send mail(To send again comment account creation)

4 - Use channelid to create a maillist/group or a Conversation

```html
<html>
<head>
<script src="eos.js"></script>
</head>
<body>
<div>
    <h2>Simple Bmail dapp</h2>
	<div style="margin: 10px;">
	<span>Sender:</span>
	<input name="sender" type="text" />
	</div>
	<div style="margin: 10px;">
	<span>Reciever:</span>
	<input name="receiver" type="text" />
	</div>
	<button onclick="sendEmail();" >Send Mail!</button>
</div>
<script>

function sendEmail() {
        var senderName = document.getElementsByName("sender")[0].value;
	var receiverName = document.getElementsByName("receiver")[0].value;

	// system account keys
	eosioPrivate = "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3";
	eosioPublic = "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV";

	// Optional configuration..
	config = {
	  keyProvider: [eosioPrivate], // WIF string or array of keys..
	  httpEndpoint: "http://localhost:8888",
	  expireInSeconds: 60,
	  broadcast: true,
	  debug: false,
	  sign: true
	};

	var eos = Eos.Localnet(config);

	//create new account for sender
	eos.newaccount({
	  creator: "eosio",
	  name: senderName,
	  owner: eosioPublic,
	  active: eosioPublic,
	  recovery: "eosio"
	  
	}).then(result => {
	  console.log(result);
	
	  //create new account for receiver
	  return eos.newaccount({
	    creator: "eosio",
	    name: receiverName,
	    owner: eosioPublic,
	    active: eosioPublic,
	    recovery: "eosio"
	  });
	  
	}).then(result => {
		console.log(result);
	
	    //send email from sender to receiver
	    return eos.transaction({
	      actions: [
	    	{
	    	  account: "bmail.code",
	    	  name: "sendmail",
	    	  authorization: [{
	    	     actor: senderName,
	    	     permission: "active"
	    	  }],
	    	  data: {
	    	     sender: senderName,
	    	     receivers: [{receiver: receiverName, mailhash: "QWERTYUIOPASDFGHJKLZXCVBNMDFGHJKLA"}],
	    	     channelid: 0
	    	  }
	    	}
	      ]
	    });
		
	}).then(result => {
	  console.log(result);
	
	  //get table rows by sender dbprefs (last_senderid, last_receiverid)
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: senderName,
		table: "dbprefs",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	
	  //get table rows by sender dbsentmail
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: senderName,
		table: "dbsentmail",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	
	  //get table rows by sender dbrecvmail
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: senderName,
		table: "dbrecvmail",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	
	  //get table rows by receiver dbprefs (last_senderid, last_receiverid)
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: receiverName,
		table: "dbprefs",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	
	  //get table rows by receiver dbsentmail
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: receiverName,
		table: "dbsentmail",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	
	  //get table rows by receiver dbrecvmail
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: receiverName,
		table: "dbrecvmail",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	});
}
</script>
</body>
</html>
```
