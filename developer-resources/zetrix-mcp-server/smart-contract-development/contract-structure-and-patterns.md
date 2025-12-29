# Contract Structure and Patterns

### **ES5 Class Pattern**

Zetrix smart contracts use ES5 JavaScript (no ES6+ features). Here's how to create classes:

```javascript
const MyContract = function() {
    const self = this;

    // Private variables (local scope)
    const _privateVar = 'private';

    // Public variables
    self.publicVar = 'public';

    // Protected namespace
    self.p = {};

    // Private method
    const _privateMethod = function() {
        return _privateVar;
    };

    // Public method
    self.publicMethod = function() {
        return self.publicVar;
    };

    // Protected method
    self.p.protectedMethod = function() {
        return _privateMethod();
    };

    // Constructor/Initialize
    self.init = function(param1, param2) {
        // Initialization logic
        self.publicVar = param1;
    };
};

// Entry point
function init(input) {
    const contract = new MyContract();
    contract.init(input);
}

function main(input) {
    // Handle contract calls
}

function query(input) {
    // Handle read-only queries
}
```

**Restrictions:**

* ❌ No arrow functions
* ❌ No let/const (use var only)
* ❌ No template literals
* ❌ No class keyword
* ❌ No ES6+ syntax

***

### **Inheritance Pattern**

```javascript
const ParentContract = function() {
    const self = this;
    self.p = {};

    self.p.parentMethod = function() {
        return 'parent';
    };
};

const ChildContract = function() {
    const self = this;

    // Call parent constructor
    ParentContract.call(self);

    // Save reference to parent method
    const _parentMethod = self.p.parentMethod;

    // Override parent method
    self.p.parentMethod = function() {
        // Add child logic
        const result = _parentMethod.call(self);
        return result + ' extended';
    };
};
```

***

### **Storage Pattern**

```javascript
const TokenContract = function() {
    const self = this;

    // Storage keys
    const TOTAL_SUPPLY = 'totalSupply';
    const BALANCE_PREFIX = 'balance_';

    self.getTotalSupply = function() {
        return Chain.load(TOTAL_SUPPLY) || '0';
    };

    self.setTotalSupply = function(amount) {
        Chain.store(TOTAL_SUPPLY, amount);
    };

    self.getBalance = function(address) {
        const key = BALANCE_PREFIX + address;
        return Chain.load(key) || '0';
    };

    self.setBalance = function(address, amount) {
        const key = BALANCE_PREFIX + address;
        Chain.store(key, amount);
    };
};
```

***
