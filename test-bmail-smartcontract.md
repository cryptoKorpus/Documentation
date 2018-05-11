# Simple bmail dapp

Test example for simple iteration with eosjs with bmail smart contract

1 - Compile eosjs lib explained here: https://github.com/EOSIO/eosjs#browser

2 - Compile eosjs-keygen explained here: https://github.com/EOSIO/eosjs-keygen#browser

3 - Change httpEndpoint to a network with a bmail smartcontract deployed by a bmail.code account

4 - Create/Login with this created accounts on the network:

tester1

Private key: 5K57oSZLpfzePvQNpsLS6NfKXLhhRARNU13q6u2ZPQCGHgKLbTA

Public key: EOS8Q8CJqwnSsV4A6HDBEqmQCqpQcBnhGME1RUvydDRnswNngpqfr

tester2

Private key: 5JcvzJQ2i9u3e64W5mjCzXCBEr2KEhi8Gmt7Qc298HfHD6Y4knK

Public key: EOS5TTSE33xyEZxfkBkNVVtNJecrw7wrq1GCRNXvm9AFzs96WRSev

tester3

Private key: 5J3dhow2RqqXccKqqAMADuJ3St6SmzisA8Edz3FrRteXiXR2yy1

Public key: EOS6Tu4bYV7fJnLxpwroVvNqdxw3ZKCqA7NWNkpiiQu6LAQgVx9fb

tester4

Private key: 5KX2dZz58kQnqjMbzPcBcJbmEUxnQv4A6WcLr9YiU2ZhMYkFDtN

Public key: EOS8it2vRbAxRLJLFqZqNVXfVKa2pFJ8v9wi5D2i7AdCN48HcjDoh

5 - Insert receiver, subject, body and send mail

6 - Use channelid to create a maillist/group or a Conversation

```html
<html>
<head>
<script src="eos.js"></script>
<script src="eosjs-keygen.js"></script>
</head>
<body onload="loadappstatus()">
<div>
    <h2>Simple Bmail dapp</h2>
	
<div id="loginform">
	<div style="margin: 10px;">
		<span>accountname:</span>
		<input name="accountname" type="text" />
	</div>
	<div style="margin: 10px;">
		<span>privatekey:</span>
		<input name="privatekey" type="password" />
	</div>
	<button onclick="login();" >login</button>
</div>
	
<div id="sendmailform">	
	<div style="margin: 10px;">
		<button onclick="logout();" >logout</button>
	</div>

	<div style="margin: 10px;">
		<span>Sender:</span>
		<span id="sender"></span>
	</div>
	<div style="margin: 10px;">
		<span>Reciever:</span>
		<input name="receiver" type="text" />
	</div>
	<div style="margin: 10px;">
		<span>subject:</span>
		<input name="mailsubject" type="text" />
	</div>
	<div style="margin: 10px;">
		<span>body:</span>
		<textarea name="mailbody" rows="4" cols="50">
		TODO not working yet!
		</textarea>
	</div>

	<button onclick="sendEmail();" >Send Mail!</button>
</div>
	
<div id="status">	
</div>	
	
</div>
<script>
var sessionTimeout = 30;

function loadappstatus() {
	var account = JSON.parse(localStorage.getItem("bmail_account"));
	var loggedin = false;
	
	if(account !== null) {
		var diffMs = new Date() - new Date(account.expireDate);
		var diffMins = Math.round(((diffMs % 86400000) % 3600000) / 60000); // minutes

		if(diffMins < sessionTimeout) {
			loggedin = true;
			
		}
		else {
			localStorage.removeItem("bmail_account");
			document.getElementById("status").innerHTML = "Account expired!";
		}
	}
	
	if(loggedin) {
		document.getElementById('loginform').style.display = 'none';
		document.getElementById('sendmailform').style.display = 'block';
		
		document.getElementById("status").innerHTML = "";
		document.getElementById("sender").innerHTML = account.name;
		document.getElementsByName("accountname")[0].value = "";
	    document.getElementsByName("privatekey")[0].value = "";
		
		mykeystore = kos.Keystore(account.name, {
			timeoutInMin: sessionTimeout,
			uriRules: {   //TODO: review permissions/rules
			  'active': '.*',
			  'active/**': '.*'
			},
		});
		
		eos = Eos.Localnet({
		  keyProvider: mykeystore.keyProvider,
		  httpEndpoint: "http://localhost:8888",
		  expireInSeconds: 60,
		  broadcast: true,
		  debug: false,
		  sign: true
		});
	}
	else {
		document.getElementById('loginform').style.display = 'block';
		document.getElementById('sendmailform').style.display = 'none';
	}
	
}

function login() {
	var accountName = document.getElementsByName("accountname")[0].value;
	var privateKey = document.getElementsByName("privatekey")[0].value;

	mykeystore = kos.Keystore(accountName, {
		timeoutInMin: sessionTimeout,
		uriRules: {   //TODO: review permissions/rules
		  'active': '.*',
		  'active/**': '.*'
		},
    });

	eos = Eos.Localnet({
	  keyProvider: mykeystore.keyProvider,
	  httpEndpoint: "http://localhost:8888",
	  expireInSeconds: 60,
	  broadcast: true,
	  debug: false,
	  sign: true
	});
	
	eos.getAccount(accountName).then(account => {
      mykeystore.deriveKeys({
        parent: privateKey,
        accountPermissions: account.permissions,
		saveKeyMatches: ["active"]
      });
	  
	  localStorage.setItem("bmail_account", JSON.stringify({
	    name: accountName,
		expireDate: new Date()
	  }));
	  
	  loadappstatus();
    }).catch(error => {
	  console.log(error);
	  document.getElementById("status").innerHTML = "login failed!";
	});
}

function logout() {
	mykeystore.logout();
	localStorage.removeItem("bmail_account");
	loadappstatus();
	document.getElementById("status").innerHTML = "logout!";
}

function sendEmail() {
    var account = JSON.parse(localStorage.getItem("bmail_account"));
	var accountName = account.name;
	var receiverName = document.getElementsByName("receiver")[0].value;
	var subject = document.getElementsByName("mailsubject")[0].value;
    var body = "QWERTYUIOPASDFGHJKLZXCVBNMDFGHJKLA"; //TODO ipfs hash for now
	
	//find receiver account and active pubkey
	eos.getAccount(receiverName).then(account => {
      var receiverPubKey = null;
	  //get receiver active pubkey
	  for(var i = 0; i < account.permissions.length; i++) {
		if(account.permissions[i].perm_name == "active") {
			//TODO so far active authority must have atleast 1 key. Create later a authority with a specific mail key
			receiverPubKey = account.permissions[i].required_auth.keys[0].key;
			break;
		}
	  }

	  var encryptedSubject = Eos.modules.ecc.Aes.encrypt(mykeystore.getPrivateKey('active'), receiverPubKey, subject);
	  
	  //send email from sender to receiver
	  return eos.transaction({
		  actions: [
			{
			  account: "bmail.code",
			  name: "sendmail",
			  authorization: [{
				 actor: accountName,
				 permission: "active"
			  }],
			  data: {
				 sender: accountName,
				 receivers: [{receiver: receiverName, nonce: encryptedSubject.nonce, subjecthash: encryptedSubject.message, mailhash: body}],
				 channelid: 0
			  }
			}
		  ]
		});	  
    }).catch(error => {
	  console.log(error);
	  document.getElementById("status").innerHTML = "receiver account does not exist!";
	})
	.then(result => {
	  console.log(result);
	
	  //get table rows by sender dbprefs (last_senderid, last_receiverid)
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: accountName,
		table: "dbprefs",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	
	  //get table rows by sender dbsentmail
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: accountName,
		table: "dbsentmail",
		limit: 50
	  })
	  
	}).then(result => {
	  console.log(result);
	
	  //get table rows by sender dbrecvmail
	  return eos.getTableRows({
		json: true,
		code: "bmail.code",
		scope: accountName,
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
