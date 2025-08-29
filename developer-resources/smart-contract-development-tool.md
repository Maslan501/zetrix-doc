# Smart Contract Development Tool

## Zetrix Development Tool

The following guide explains how to use our [Zetrix development tool](https://github.com/Zetrix-Chain/zetrix-development-tool), built for professionals. It facilitates performing frequent tasks, such as smart contract deployment and testing.

### Getting Started

For the purpose of this guide, clone the [repository](https://github.com/Zetrix-Chain/zetrix-development-tool) to your local machine. It will be used a base project for your own smart contract development on Zetrix.

After cloning the repository, we install the NPM packages for this project by running the following command:

```shell
npm install
```

Then create a `.env` file in the root of your project and insert your key/value pairs in the following format of `KEY=VALUE`:

If you're using the Zetrix testnet:

```sh
NODE_URL=test-node.zetrix.com
```

If you're using the Zetrix mainnet:

```shell
NODE_URL=node.zetrix.com
```

Insert the Zetrix address you are going to use to deploy the smart contract from

```sh
ZTX_ADDRESS=<YOUR ZETRIX ADDRESS>
```

Insert the private key of the Zetrix address you are going to use to deploy the smart contract from

```sh
PRIVATE_KEY=<THE PRIVATE KEY TO YOUR ZETRIX ADDRESS>
```

#### Contract development

The smart contract components can be found in the **contracts** directory. If you change the filename(s), please modify the contract name in **scripts** directory as well.

**Manual for running script:**

```sh
npm run help
```

**Contract deployment:**

```sh
npm run deploy:<NAMING_REFER_TO_PACKAGE_JSON>
```

**Contract upgrade:**

```sh
npm run upgrade:<NAMING_REFER_TO_PACKAGE_JSON>
```

**Run test with coverage:**

```sh
npm run test-coverage tests/unit/<TEST_CASE>.js
```

**Run integration test:**

```sh
npm test tests/integration/<TEST_CASE>.js
```

#### Development Guide

The development of Zetrix smart contract is using Javascript ES5 which has less support on OOP concepts such as `class`. Hence, we imitate the OOP implementation by using functionalities available in ES5.

**Class**

OOP implementation

```java
class Example {
    
    Example(String param) {
        // constructor
    }
}

Example exampleInst = new Example(param);
```

ES5 Javascript implementation

```javascript
const Example = function () {
    
    const self = this; // keep the context
    
    self.init = function (param) {
        // constructor
    };
};

const exampleInst = new Example();
exampleInst.init(param);
```

**Managing private, protected and public method**

OOP implementation

```java
class Example {
    
    private void privateMethod() {
        // private method
    }
    
    public void publicMethod() {
        // public method
    }

    protected void protectedMethod() {
        // protected method
    }
}
```

ES5 Javascript implementation

```javascript
const Example = function () {
    
    const self = this; // keep the context

    self.p = {/*protected function*/};
    
    const _privateMethod = function () {
        // private method
    };
    
    self.publicMethod = function () {
        // public method
    };
    
    self.p.protectedMethod = function () {
        // protected method : this method is similar to the public method, but we just defined in `p` nameclass to differentiate  
    };
};
```

**Inheritance and override**

OOP implementation

```java
class ExampleParent {
    
    public void parentMethod1() {
        
    }

    public void parentMethod2() {

    }
    
    public void parentMethod3(int a, int b) {
        
    }
}

class ExampleChild extends ExampleParent {
    
    @Override
    public void parentMethod1() {
        // Override parent method
        // Do something else and continue with original parentMethod
        super.parentMethod1();
    }

    @Override
    public void parentMethod2() {
        // Override parent method
    }
    
    private void childMethod(int a, int b) {
        // Use parent function in child wrapper
        return super.parentMethod3(a, b);
    } 
}
```

ES5 Javascript implementation

```javascript
const ExampleParent = function () {

    const self = this; // keep the context

    self.p = {/*protected function*/};
    
    self.p.parentMethod1 = function () {

    };
    
    self.p.parentMethod2 = function() {
        
    };
    
    self.parentMethod3 = function(a, b) {
        
    };
};

const ExampleChild = function () {

    const self = this;

    ExampleParent.call(self); // Inherit
    
    const _parentMethod1 = self.p.parentMethod1
    self.p.parentMethod1 = function(){
        // Override parent method
        // Do something else and continue with original parentMethod
        _parentMethod1.call(self);
    };
    
    self.p.parentMethod2 = function() {
        // Override parent method
    };

    const _childMethod = function() {
        // Use parent function in child wrapper
        return self.parentMethod3(a, b);
    };

};
```

This is the end of the tutorial. Happy buidling!
