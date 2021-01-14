# Home-Sale
บทนำและวัตถุประสงค์

```
Home-Sale by Sofia เป็นธุรกิจขายบ้านที่นำ Smart Contract มาใช้ เพื่อพัฒนาธุรกิจให้ง่าย สะดวก รวดเร็ว โปร่งใส และมีความปลอดภัย สำหรับผู้ใช้ที่ต้องการดูข้อมูลและต้องการซื้อบ้าน  โดยมีขั้นตอนดังต่อไปนี้
```

## 1. กำหนดค่าสิ่งแวดล้อม
สร้างไดเร็กทอรีสำหรับบันทึกโปรเจ็คนี้ เช่น ใช้คำสั่งต่อไปนี้เพื่อสร้างและย้ายเข้าไปยังไดเร็กทอรี่ชื่อ home-sale

```
mkdir home-sale
cd home-sale
```

ดาวน์โหลดโครงสร้างของโปรเจ็ค home-sale ซึ่งมีอยู่ใน Truffle Framework โดยใช้คำสั่งต่อไปนี้

```
truffle unbox pet-shop
```

ทดลองใช้คำสั่ง ```ls -l``` เพื่อดูโครงสร้างของโปรเจ็ค สังเกตว่า มีไดเร็กทอรี่และไฟล์ที่สำคัญต่อไปนี้

- contracts: เป็นไดเร็กทอรีที่ใช้เก็บ Smart Contracts ที่เขียนด้วยภาษา Solidity
- migrations: เป็นไดเร็กทอรีที่ใช้เก็บไฟล์ JavaScript ซึ่งเป็นโค้ดที่ใช้ในการจัดการ Smart Contracts ให้ลงไปยังบล็อกเชน
- src: เป็นไดเร็กทอรีที่ใช้เก็บไฟล์ที่เกี่ยวข้องกับ Web Application เช่น JavaScript, CSS, HTML, images เป็นต้น
- test: เป็นไดเร็กทอรีใช้ที่เก็บไฟล์ Solidity หรือ JavaScript ก็ได้ ที่ใช้เพื่อทดสอบ Smart Contracts
- truffle-config.js: คือไฟล์ที่ใช้ในการกำหนดค่าของโปรเจ็ค

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

#### 2.2. compile และ migrate
ทำการคอมไพล์ Smart Contracts โดยใช้คำสั่ง

```
truffle compile
```

โปรดตรวจสอบว่า สามารถคอมไพล์ได้สำเร็จก่อนทำในขั้นตอนต่อไป

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

ต่อไปคือ การรันการทดสอบที่เขียนไว้ในไดเร็กทอรี test โดยใช้คำสั่งต่อไปนี้

```
truffle test
```

ผลการทดสอบผ่านทั้ง 3 กรณีจะได้ผลลัพธ์ดังรูปต่อไปนี้

[![runtest.jpg](https://i.postimg.cc/GpT6Y8Sw/runtest.jpg)](https://postimg.cc/WDj9c3vX)


## 3. รูปแบบ UI เพื่อใช้เชื่อมต่อกับผู้ใช้
ส่วนติดต่อกับผู้ใช้ เป็นดังรูปต่อไปนี้



สร้างผลลัพธ์เช่นรูปข้างต้นได้โดยใช้ไฟล์ ```src/index.html``` โปรดเปิดไฟล์นี้โดยใช้ Visual Studio Code และสำรวจโครงสร้างของไฟล์ สังเกตได้ว่า มีส่วนที่เป็น Template ในขณะที่ข้อมูลที่ใช้ในการแสดงผลจะถูกกำหนดโดยส่วน Backend

## 4. สร้าง Backend ที่สามารถเชื่อมต่อกับ Smart Contract
แก้ไขไฟล์ ```src/js/app.js``` ให้มีโค้ดดังนี้
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

- จากนั้นทำการย้ายจาก Ethereum Mainnet มาที่ Ganache โดยคลิกที่ Ethereum Mainnet แล้วเลือก Custom RPC

- ป้อนข้อมูล Network Name (เป็นค่าใดๆ ก็ได้) ส่วน New RPC URL ต้องเป็น URL ของ Ganache ซึ่งในที่นี้คือ ```http://127.0.0.1:7545```

## 6. รันโปรแกรม 

รัน Backend โดยใช้คำสั่ง 

```
npm run dev
```

จากนั้นเปิด Firefox ที่ URL ดังนี้ ```http://localhost:3000```























