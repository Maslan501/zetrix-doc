# Best Practices

## **Security**

### **Input Validation**

```javascript
Utils.assert(Utils.addressCheck(to), 'Invalid address');
Utils.assert(Utils.stoI256Check(amount), 'Invalid amount');
Utils.assert(Utils.int256Compare(amount, '0') > 0, 'Amount must be positive');
```

### **Access Control**

```javascript
const owner = Chain.load('owner');
Utils.assert(Chain.msg.sender === owner, 'Only owner');
```

### **Reentrancy Protection**

```javascript
Utils.assert(!Chain.load('locked'), 'Reentrant call');
Chain.store('locked', 'true');
// ... operations ...
Chain.store('locked', 'false');
```

***

## **Gas Optimization**

### **Minimize Storage**

```javascript
// Bad: Multiple stores
Chain.store('data1', value1);
Chain.store('data2', value2);

// Good: Single combined store
const data = JSON.stringify({data1: value1, data2: value2});
Chain.store('combined', data);
```

### **Use Local Variables**

```javascript
// Bad: Multiple loads
if (Chain.load('balance_' + addr) > amount) {
    Chain.store('balance_' + addr, Chain.load('balance_' + addr) - amount);
}

// Good: Load once
const balance = Chain.load('balance_' + addr);
if (balance > amount) {
    Chain.store('balance_' + addr, balance - amount);
}
```

***

## **Code Organization**

### **Separate Concerns**

```javascript
// Storage layer
const Storage = function() {
    const self = this;
    self.get = function(key) { return Chain.load(key); };
    self.set = function(key, val) { Chain.store(key, val); };
};

// Business logic layer
const Logic = function() {
    const self = this;
    const storage = new Storage();
    // ... use storage
};
```

### **Clear Error Messages**

```javascript
Utils.assert(condition, 'Clear error message explaining what went wrong');
```

### **Event Logging**

```javascript
// Always log important state changes
Chain.tlog('StateChanged', oldValue, newValue, timestamp);
```

***
