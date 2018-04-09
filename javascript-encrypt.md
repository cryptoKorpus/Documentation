Example of sending a message with private eosjs between Alice and Bob. The way it works is different from the RSA assymetric keys 
(nrs big prime) that we know of. It is done through a symmetric key created between privateAlice for publicaBob and then it is
possible alice or Bob to disclose with the inverse pair of private and public.
ECC-based keys (elliptic curve) have the property of generating the public through the private but obviously not the opposite. ???
So it WORKS !!

´´´
function testPrivateMessageSend(text) {

  console.log(text);

  var privateWifAlice = "";
  var privateWifBob = "";
  
  Eos.modules.ecc.PrivateKey.randomKey()
    .then(privateKey => {
privateWifAlice = privateKey.toWif(); 
return Eos.modules.ecc.PrivateKey.randomKey();
    })
.then(privateKey => {
    privateWifBob = privateKey.toWif();
var publicWifAlice = Eos.modules.ecc.privateToPublic(privateWifAlice);
var publicWifBob = Eos.modules.ecc.privateToPublic(privateWifBob);
  
var encrypted = Eos.modules.ecc.Aes.encrypt(privateWifAlice, publicWifBob, text);
  
console.log(encrypted);
console.log(encrypted.message.toString());
//send information thru the wire
// alice reads msg
var decriptedAlice = Eos.modules.ecc.Aes.decrypt(privateWifAlice, publicWifBob, encrypted.nonce, encrypted.message, encrypted.checksum);
  
// bob reads msg
var decriptedBob = Eos.modules.ecc.Aes.decrypt(privateWifBob, publicWifAlice, encrypted.nonce, encrypted.message, encrypted.checksum);
console.log("alice decrypt: " + decriptedAlice.toString());
console.log("bob decrypt: " + decriptedBob.toString());
});
}
´´´
