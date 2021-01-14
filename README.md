# Homes-Sale
## บทนำ (Assignment 3)

Home-Sale by Sofia เป็นธุรกิจขายบ้านที่นำ Smart Contract มาใช้ เพื่อพัฒนาธุรกิจให้ง่าย สะดวก รวดเร็ว โปร่งใส และมีความปลอดภัย สำหรับผู้ใช้ที่ต้องการดูข้อมูลและรายละเอียดเกี่ยวกับบ้าน สามารถดูข้อมูลได้จากทางเว็บไซต์ Homes Sale โดยไม่ต้องเสียเวลาเดินทางเข้ามาสอบถามรายละเอียดด้วยตนเองโดยงตรง ทั้งยังสามารถดูได้หลายโครงการไม่ต้องเสียเวลาเดินทางหลายที่ ประกอบกับสถานะการณ์โควิดช่วงนี้ด้วย หากผู้ใช้ดูรายละเอียดข้อมูลแล้วต้องการเยี่ยมชมโครงการ ทางโครงการก็จะจัดตารางคิวแล้วนัดวันเวลาอย่างเหมาะสม แล้วนัดหรือทำการซื้อบ้านได้หากผู้ใช้ตกลง 

โดยทางโครงการได้นำโปรเจค pet-shop จากที่อาจารย์ได้สอนมาพัฒนา ปรับเปลี่ยนให้เป็นเว็บไซต์ home-sale โดยมีขั้นตอนดังต่อไปนี้


## 1. กำหนดค่าสิ่งแวดล้อม
#### 1.1 สร้างไดเร็กทอรีสำหรับบันทึกโปรเจ็คนี้ เช่น ใช้คำสั่งต่อไปนี้เพื่อสร้างและย้ายเข้าไปยังไดเร็กทอรี่ชื่อ home-sale ไว้ใน DApp ซึ่งอยู่ใน Document

```
mkdir home-sale
cd home-sale
```

#### 1.2 ดาวน์โหลดโครงสร้างของโปรเจ็ค pet-shop ซึ่งมีอยู่ใน Truffle Framework โดยใช้คำสั่งต่อไปนี้

```
truffle unbox pet-shop
```

#### 1.3 ใช้คำสั่ง ```ls -l``` เพื่อดูโครงสร้างของโปรเจ็ค สังเกตว่า มีไดเร็กทอรี่และไฟล์ที่สำคัญต่อไปนี้

- contracts: เป็นไดเร็กทอรีที่ใช้เก็บ Smart Contracts ที่เขียนด้วยภาษา Solidity
- migrations: เป็นไดเร็กทอรีที่ใช้เก็บไฟล์ JavaScript ซึ่งเป็นโค้ดที่ใช้ในการจัดการ Smart Contracts ให้ลงไปยังบล็อกเชน
- src: เป็นไดเร็กทอรีที่ใช้เก็บไฟล์ที่เกี่ยวข้องกับ Web Application เช่น JavaScript, CSS, HTML, images เป็นต้น
- test: เป็นไดเร็กทอรีใช้ที่เก็บไฟล์ Solidity หรือ JavaScript ก็ได้ ที่ใช้เพื่อทดสอบ Smart Contracts
- truffle-config.js: คือไฟล์ที่ใช้ในการกำหนดค่าของโปรเจ็ค

#### 1.4 เปิด Visual Studio Code ที่ VirtualBox โดยไปที่ ``` File/Open Folder/Document/DApp/home-sale``` แล้วกด OK


## 2. สร้าง Smart Contract
#### 2.1. Adoption Smart Contract

ใช้ Visual Studio Code เพื่อสร้างไฟล์ชื่อ Adoption.sol ในไดเร็กทอรี contracts โดยมีโค้ดดังนี้

```
pragma solidity ^0.5.0;
contract Adoption {
    address[16] public adopters;
    function adopt(uint petId) public returns (uint) {
        require(petId >= 0 && petId <=15);
        adopters[petId] = msg.sender;
        return petId;
    }
    function getAdopters() public view returns (address[16] memory) {
        return adopters;
    }
}
```

#### 2.2. Compile และ Migrate
ทำการคอมไพล์ Smart Contracts โดยใช้คำสั่ง

```
truffle compile
```

ตรวจสอบว่า สามารถคอมไพล์ได้สำเร็จก่อนทำในขั้นตอนต่อไป

ใช้ Visual Studio Code ในการสร้างไฟล์ 2_deploy_contracts.js ในไดเร็กทอรี migrations ดังนี้

```
var Adoption = artifacts.require("Adoption");
module.exports = function(deployer) {
  deployer.deploy(Adoption);
};
```

เปิดโปรแกรม Ganache โดยการใช้เมาส์ดับเบิลคลิกที่ชื่อไฟล์ จากนั้นคลิกที่ NF507 (ในกรณีที่ใช้งานครั้งแรกให้คลิกที่ New Workspace ครั้งต่อไปไม่จำเป็นต้องสร้าง Workspace ใหม่ทุกครั้ง)

[![Ganache-NF507.png](https://i.postimg.cc/R0zHSMv4/Ganache-NF507.png)](https://postimg.cc/9DJM1H4n)

ผลลัพธ์ที่ได้เป็นดังรูป โดย Ganache สร้างบัญชีให้ 10 บัญชี แต่ละบัญชีมี 100 ETH โดยดีฟอล์ต

[![ganache.jpg](https://i.postimg.cc/MGFJ0GX8/ganache.jpg)](https://postimg.cc/QB1zhhp6)

เมื่อ Ganache ทำงานได้ดังรูปข้างต้น ขั้นตอนต่อไปคือการ migrate ทำได้โดยใช้คำสั่งต่อไปนี้

```
truffle migrate
```

#### 2.3. ทดสอบ Smart Contract
ใช้ Visual Studio Code ในการสร้างไฟล์ TestAdoption.sol เพื่อทดสอบ Adoption.sol และบันทึกลงในไดเร็กทอรี test 

```
pragma solidity ^0.5.0;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/Adoption.sol";

contract TestAdoption {
  // The address of the adoption contract to be tested
  Adoption adoption = Adoption(DeployedAddresses.Adoption());
  
  // The id of the pet that will be used for testing
  uint expectedPetId = 8;
  
  //The expected owner of adopted pet is this contract
  address expectedAdopter = address(this);
  
  function testUserCanAdoptPet() public {
    uint returnedId = adoption.adopt(expectedPetId);
    Assert.equal(returnedId, expectedPetId, "Adoption of the expected pet should match what is returned.");
  }
  
  // Testing retrieval of a single pet's owner
  function testGetAdopterAddressByPetId() public {
    address adopter = adoption.adopters(expectedPetId);
    Assert.equal(adopter, expectedAdopter, "Owner of the expected pet should be this contract");
  }
  
  // Testing retrieval of all pet owners
  function testGetAdopterAddressByPetIdInArray() public {
    // Store adopters in memory rather than contract's storage
    address[16] memory adopters = adoption.getAdopters();
    Assert.equal(adopters[expectedPetId], expectedAdopter, "Owner of the expected pet should be this contract");
  }
}
```

#### การรันการทดสอบที่เขียนไว้ในไดเร็กทอรี test โดยใช้คำสั่งต่อไปนี้

```
truffle test
```

#### ผลการทดสอบผ่านทั้ง 3 กรณีจะได้ผลลัพธ์ดังรูปต่อไปนี้

[![runtest.jpg](https://i.postimg.cc/GpT6Y8Sw/runtest.jpg)](https://postimg.cc/WDj9c3vX)


## 3. รูปแบบ UI (Front-End) เพื่อใช้เชื่อมต่อกับผู้ใช้
ส่วนติดต่อกับผู้ใช้ เป็นดังรูปต่อไปนี้

[![Final.jpg](https://i.postimg.cc/7ZgV7WCq/Final.jpg)](https://postimg.cc/sMg7CK9L)
[![Final1.jpg](https://i.postimg.cc/mDdVk1PM/Final1.jpg)](https://postimg.cc/gXZ3BJqk)
[![Final2.jpg](https://i.postimg.cc/7hf92PzR/Final2.jpg)](https://postimg.cc/p5MKNHqQ)
[![Final3.jpg](https://i.postimg.cc/QNy0r5GZ/Final3.jpg)](https://postimg.cc/t7Fh3s4B)


สร้างผลลัพธ์เช่นรูปข้างต้นได้โดยใช้ไฟล์ ```src/index.html``` เปิดไฟล์นี้โดยใช้ Visual Studio Code และสำรวจโครงสร้างของไฟล์ สังเกตได้ว่า มีส่วนที่เป็น Template ในขณะที่ข้อมูลที่ใช้ในการแสดงผลจะถูกกำหนดโดยส่วน Backend

```
<!DOCTYPE html>
<html lang="en">
 <head>
   <meta charset="utf-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1">
   <!-- The above 3 meta tags *must* come first in the head; any other head content must come *after* these tags -->
   <title>Homes Sale</title>
 
   <!-- Bootstrap -->
   <link href="css/bootstrap.min.css" rel="stylesheet">
 
   <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
   <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
   <!--[if lt IE 9]>
     <script src="https://oss.maxcdn.com/html5shiv/3.7.3/html5shiv.min.js"></script>
     <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
   <![endif]-->
 </head>
 <body>
   <div class="container">
     <div class="row">
       <div class="col-xs-15 col-sm-8 col-sm-push-2">
         <br>
         <h1 class="text-center" style="color: blue; font-size: 60px;"><strong>Homes Sale</strong></h1>
         <hr/>
         <br/>
       </div>
     </div>
 
     <div id="petsRow" class="row">
       <!-- PETS LOAD HERE -->
     </div>
   </div>
 
   <div id="petTemplate" style="display: none;">
     <div class="col-sm-6 col-md-6 col-lg-4">
       <div class="panel panel-default panel-pet">
         <div class="panel-heading" style="background-color: rgb(215, 240, 215);">
           <h3 class="panel-title" style= "color: black; font-size: 20px; font-weight: 1000;"><strong>Scrappy</strong></h3>
         </div>
         <div class="panel-body">
           <img alt="140x140" data-src="holder.js/140x140" class="img-rounded img-center" style="width: 100%;" src="https://animalso.com/wp-content/uploads/2017/01/Golden-Retriever_6.jpg" data-holder-rendered="true">
           <br/><br/>
           <strong>Detail</strong>: <span class="pet-breed">Golden Retriever</span><br/>
           <strong>Price</strong>: <span class="pet-age">3</span><br/>
           <strong>Location</strong>: <span class="pet-location">Warren, MI</span><br/><br/>
           <button class="btn btn-default btn-adopt" style="color: blue; font-weight: 900;" type="button" data-id="0"><strong>Buy</strong></button>
         </div>
       </div>
     </div>
   </div>
 
   <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
   <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
   <!-- Include all compiled plugins (below), or include individual files as needed -->
   <script src="js/bootstrap.min.js"></script>
   <script src="js/web3.min.js"></script>
   <script src="js/truffle-contract.js"></script>
   <script src="js/app.js"></script>
 </body>
</html>
```

ในโค้ดนี้ได้เปลี่ยนข้อมูลส่วนที่นำไปแสดงผลในรายละเอียดของ pet-shop 
- จาก breed เป็น Detail ซึ่งบอกถึงรายละเอียดข้อมูลบ้านแต่ละหลังใน home-sale
- จาก age เป็น Price ซึ่งบอกถึงราคาบ้านแต่ละหลังใน home-sale
- ส่วน Location ไม่ได้ทำการเปลี่ยน เพื่อให้แสดงตำแหน่งที่อยู่บ้านแต่ละหลังใน home-sale
- เปลี่ยนชื่อเว็บไซต์จาก Pet's Pet Shop เป็น Homes Sale
- เปลี่ยนชื่อเว็บไซต์ให้เป็นสีน้ำเงิน
- เปลี่ยนขนาดตัวอักษรให้ใหญ่ขี้น
- เปลี่ยน background หัวเรื่องชื่อหมู่บ้านแต่ละหลังให้มีสีสัน (สีเขียวอ่อน), สีให้เป็นสีดำม ขนาดตัวอักษรให้ใหญ่ และมีความหนาขึ้น
- เปลี่ยนขนาดของรูปภาพให้มีขนาดใหญ่ขึ้น 
- เปลี่ยนตัวอักษรจาก Adopt เป็น Buy และทำให้ตัวอักษาหนาขึ้น และเปลี่ยนสีของตัวอักษาให้ป็นสีน้ำเงิน


## 4. สร้าง Backend ที่สามารถเชื่อมต่อกับ Smart Contract
#### 4.1 แก้ไขไฟล์ ```src/js/app.js``` ให้มีโค้ดดังนี้

```
App = {
 web3Provider: null,
 contracts: {},
 
 init: async function() {
   // Load pets.
   $.getJSON('../pets.json', function(data) {
     var petsRow = $('#petsRow');
     var petTemplate = $('#petTemplate');
 
     for (i = 0; i < data.length; i ++) {
       petTemplate.find('.panel-title').text(data[i].name);
       petTemplate.find('img').attr('src', data[i].picture);
       petTemplate.find('.pet-breed').text(data[i].breed);
       petTemplate.find('.pet-age').text(data[i].age);
       petTemplate.find('.pet-location').text(data[i].location);
       petTemplate.find('.btn-adopt').attr('data-id', data[i].id);
 
       petsRow.append(petTemplate.html());
     }
   });
 
   return await App.initWeb3();
 },
 
 initWeb3: async function() {
   // Modern dapp browsers...
   if (window.ethereum) {
     App.web3Provider = window.ethereum;
     try {
       // Request account access
       await window.ethereum.enable();
     } catch (error) {
       // User denied account access...
       console.error("User denied account access")
     }
   }
   // Legacy dapp browsers...
   else if (window.web3) {
     App.web3Provider = window.web3.currentProvider;
   }
   // If no injected web3 instance is detected, fall back to Ganache
   else {
     App.web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
   }
   web3 = new Web3(App.web3Provider);
 
   return App.initContract();
 },
 
 initContract: function() {
   $.getJSON('Adoption.json', function (data) {
     // Get the necessary contract artifact file and instantiate it with @truffle/contract
     var AdoptionArtifact = data;
     App.contracts.Adoption = TruffleContract(AdoptionArtifact);
 
     // Set the provider for our contract
     App.contracts.Adoption.setProvider(App.web3Provider);
 
     // Use our contract to retrieve and mark the adopted pets
     return App.markAdopted();
   });
   return App.bindEvents();
 },
 
 bindEvents: function() {
   $(document).on('click', '.btn-adopt', App.handleAdopt);
 },
 
 markAdopted: function() {
   var adoptionInstance;
 
   App.contracts.Adoption.deployed().then(function (instance) {
     adoptionInstance = instance;
 
     return adoptionInstance.getAdopters.call();
   }).then(function (adopters) {
     for (i = 0; i < adopters.length; i++) {
       if (adopters[i] !== '0x0000000000000000000000000000000000000000') {
         $('.panel-pet').eq(i).find('button').text('Sold Out').attr('disabled', true);
       }
     }
   }).catch(function (err) {
     console.log(err.message);
   });
 },
 
 handleAdopt: function(event) {
   event.preventDefault();
 
   var petId = parseInt($(event.target).data('id'));
 
   var adoptionInstance;
 
   web3.eth.getAccounts(function (error, accounts) {
     if (error) {
       console.log(error);
     }
 
     var account = accounts[0];
 
     App.contracts.Adoption.deployed().then(function (instance) {
       adoptionInstance = instance;
 
       // Execute adopt as a transaction by sending account
       return adoptionInstance.adopt(petId, { from: account });
     }).then(function (result) {
       return App.markAdopted();
     }).catch(function (err) {
       console.log(err.message);
     });
   });
 }
 
};
 
$(function() {
 
 
 $(window).load(function() {
   App.init();
 });
});
```

ในโค้ดนี้ได้เปลี่ยนข้อมูลส่วนที่นำไปแสดงผลในรายละเอียดของ pet-shop ที่ button
จาก Success เป็น Sold Out ซึ่งบอกสถานะของบ้านแต่ละหลังใน home-sale กรณีที่มีผู้ซื้อไปแล้ว


#### 4.2 อัพเดทข้อมูลในไฟล์ pets.json ซึ่งอยู่ใน folder ของ src ให้เป็นดังนี้

```
[
 {
   "id": 0,
   "name": "Mera Village",
   "picture": "images/B1.jpg",
   "age": "12,000,000 THB",
   "breed": "5 Bedrooms/ 5 Bathrooms/ Area 450 Sq.m.",
   "location": "Rama 9, Bangkok"
 },
 {
   "id": 1,
   "name": "Suksawat Village",
   "picture": "images/B2.jpg",
   "age": "4,500,000 THB",
   "breed": "4 Bedrooms/ 3 Bathrooms/ Area 320 Sq.m.",
   "location": "Srinakarin, Bangkok"
 },
 {
   "id": 2,
   "name": "Linlada Village",
   "picture": "images/B3.jpg",
   "soze": "3,900,000 THB",
   "breed": "3 Bedrooms/ 2 Bathrooms/ Area 340 Sq.m.",
   "location": "Bangkapi, Bangkok"
 },
 {
   "id": 3,
   "name": "Malia Village",
   "picture": "images/B4.jpg",
   "age": "5,200,000 THB",
   "breed": "5 Bedrooms/ 4 Bathrooms/ Area 360 Sq.m.",
   "location": "Bangna, Samut Prakan"
 },
 {
   "id": 4,
   "name": "Tipawan Village",
   "picture": "images/B5.jpg",
   "age": "3,400,000 THB",
   "breed": "3 Bedrooms/ 3 Bathrooms/ Area 220 Sq.m.",
   "location": "Ramkhamhaeng, Bangkok"
 },
 {
   "id": 5,
   "name": "Sripana Village",
   "picture": "images/B6.jpg",
   "age": "3,700,000 THB",
   "breed": "3 Bedrooms/ 2 Bathrooms/ Area 340 Sq.m.",
   "location": "Ratchadaphisek, Bangkok"
 },
 {
   "id": 6,
   "name": "Supalai Village",
   "picture": "images/B7.jpg",
   "age": "9,200,000 THB",
   "breed": "4 Bedrooms/ 4 Bathrooms/ Area 380 Sq.m.",
   "location": "Din Daena, Bangkok"
 },
 {
   "id": 7,
   "name": "Casa Village",
   "picture": "images/B8.jpg",
   "age": "5,900,000 THB",
   "breed": "4 Bedrooms/ 3 Bathrooms/ Area 410 Sq.m.",
   "location": "Bang Bua Thong, Nonthaburi"
 },
 {
   "id": 8,
   "name": "Iyara Village",
   "picture": "images/B9.jpg",
   "age": "6,120,000 THB",
   "breed": "4 Bedrooms/ 4 Bathrooms/ Area 400 Sq.m.",
   "location": "Bang Kruai, Nonthaburi"
 },
 {
   "id": 9,
   "name": "Nantana Village",
   "picture": "images/B10.jpg",
   "age": "3,800,000 THB",
   "breed": "3 Bedrooms/ 3 Bathrooms/ Area 440 Sq.m.",
   "location": "Changwattana, Nonthaburi"
 },
 {
   "id": 10,
   "name": "Bua Village",
   "picture": "images/B11.jpg",
   "age": "4,100,000 THB",
   "breed": "4 Bedrooms/ 4 Bathrooms/ Area 420 Sq.m.",
   "location": "Rama 2, Bangkok"
 },
 {
   "id": 11,
   "name": "Happy Village",
   "picture": "images/B12.jpg",
   "age": "7,500,000 THB",
   "breed": "5 Bedrooms/ 3 Bathrooms/ Area 480 Sq.m.",
   "location": "Rama 3, Bangkok"
 }
]
```


#### 4.3 เพิ่มไฟล์รูปภาพบ้านใน images ซึ่งอยู่ใน folder ของ src 12 ไฟล์ มีชื่อไฟล์ดังนี้

```
 1. B1.jpg
 2. B2.jpg
 3. B3.jpg
 4. B4.jpg
 5. B5.jpg
 6. B6.jpg
 7. B7.jpg
 8. B9.jpg
 9. B10.jpg
10. B11.jpg
11. B12.jpg
12. B13.jpg
```


## 5. ดาวน์โหลดและติดตั้ง MetaMask
- ดาวน์โหลดและติดตั้ง MetaMask ที่บราวเซอร์ Firefox

[![downloadmeta.png](https://i.postimg.cc/GtLcqmv3/downloadmeta.png)](https://postimg.cc/hzYWGgCN)

[![MataFi.jpg](https://i.postimg.cc/pXn2C4FF/MataFi.jpg)](https://postimg.cc/F7N5sPGr)



- เมื่อเริ่มใช้งาน MetaMask จะได้ดังรูป

[![getmeta.jpg](https://i.postimg.cc/cHrQbdZW/getmeta.jpg)](https://postimg.cc/2bNbqRV9)

- คลิกที่ Get Started จะได้ผลลัพธ์ดังรูปด้านล่าง

[![import.jpg](https://i.postimg.cc/J7QpHzn6/import.jpg)](https://postimg.cc/Dm8rtF1L)


- คลิกที่ Import Wallet เพื่อเชื่อมต่อ MetaMask เข้ากับ Wallet ของ Ganache

[![importmetamask.jpg](https://i.postimg.cc/c1KQ013t/importmetamask.jpg)](https://postimg.cc/K42K52Jm)


- ทำการก็อปปี้ Seed จาก Ganache นำมาวางลงในช่อง Wallet Seed ตั้งพาสเวิร์ด ติ๊กที่ I have read and agree to the Terms of Use แล้วคลิก Import

[![Screen-Shot-2564-01-14-at-15-09-58.png](https://i.postimg.cc/7ZP5br90/Screen-Shot-2564-01-14-at-15-09-58.png)](https://postimg.cc/9rSmNSvQ)

- จากนั้นทำการย้ายจาก Ethereum Mainnet มาที่ Ganache โดยคลิกที่ Ethereum Mainnet แล้วเลือก Custom RPC

[![Screen-Shot-2564-01-14-at-15-11-10.png](https://i.postimg.cc/fLK987wS/Screen-Shot-2564-01-14-at-15-11-10.png)](https://postimg.cc/47Yyd9HX)

- ป้อนข้อมูล Network Name (เป็นค่าใดๆ ก็ได้ ตามรูปนี้ตั้งชื่อเป็น Ganach) ส่วน New RPC URL ต้องเป็น URL ของ Ganache ซึ่งในที่นี้คือ ```http://127.0.0.1:7545``` แล้วกด Save เพื่อบันทึก

## 6. รันโปรแกรม 

รัน Backend โดยใช้คำสั่ง 

```
npm run dev
```

จากนั้นเปิด Firefox ที่ URL ดังนี้ ```http://localhost:3000```

## แสดงหน้าจอ ดังนี้

[![Final.jpg](https://i.postimg.cc/7ZgV7WCq/Final.jpg)](https://postimg.cc/sMg7CK9L)
[![Final1.jpg](https://i.postimg.cc/mDdVk1PM/Final1.jpg)](https://postimg.cc/gXZ3BJqk)
[![Final2.jpg](https://i.postimg.cc/7hf92PzR/Final2.jpg)](https://postimg.cc/p5MKNHqQ)
[![Final3.jpg](https://i.postimg.cc/QNy0r5GZ/Final3.jpg)](https://postimg.cc/t7Fh3s4B)





#### สังเกตกรณีบ้านที่มีการซื้อไปแล้ว สถานะจะเปลี่ยนจาก Buy เป็น Sold Out
