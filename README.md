# 4 Design Patterns You Should Know for Web Development: Observer, Singleton, Strategy, and Decorator

*By Milecia McGregor*

This is a careful reading of the article [4 Design Patterns You Should Know for Web Development: Observer, Singleton, Strategy, and Decorator](https://www.freecodecamp.org/news/4-design-patterns-to-use-in-web-development/) by Milecia McGregor.

> There are 23 official patterns from the book Design Patterns - Elements of Reusable Object-Oriented Software, which is considered one of the most influential books on object-oriented theory and software development.
> 
> In this article, I'm going to cover four of those design patterns just to give you some insight to what a few of the patterns are and when you would use them.

## The Singleton Design Pattern

- One single instance of a class or an object
- It uses a global variable to store that one instance
- Prevents multiple instances and, hence, weird bugs
- Usually implemented in the constructor
- Aims at regulating the global state of an application

### Example: Logger

#### The Singleton Class

```js
class FoodLogger {
  constructor() {
    this.foodLog = []
  }
    
  log(order) {
    this.foodLog.push(order.foodItem)
    // do fancy code to send this log somewhere
  }
}

// this is the singleton
class FoodLoggerSingleton {
  constructor() {
    if (!FoodLoggerSingleton.instance) {
      FoodLoggerSingleton.instance = new FoodLogger()
    }
  }
  
  getFoodLoggerInstance() {
    return FoodLoggerSingleton.instance
  }
}

module.exports = FoodLoggerSingleton
```

#### Using the class

```js
const FoodLogger = require('./FoodLogger')

const foodLogger = new FoodLogger().getFoodLoggerInstance()

class Customer {
  constructor(order) {
    this.price = order.price
    this.food = order.foodItem
    foodLogger.log(order)
  }
  
  // other cool stuff happening for the customer
}

module.exports = Customer
```

```js
const FoodLogger = require('./FoodLogger')

const foodLogger = new FoodLogger().getFoodLoggerInstance()

class Restaurant {
  constructor(inventory) {
    this.quantity = inventory.count
    this.food = inventory.foodItem
    foodLogger.log(inventory)
  }
  
  // other cool stuff happening at the restaurant
}

module.exports = Restaurant
```

## The Strategy Design Pattern

- Like an advanced version of an if else statement
- you have an interface for a method
- the interface is then used to find the right implemenation of the method in a derived class
- useful in situations where you have required and optional methods for a class
- instead of the client looking for an implementation, it delegates to a strategy interface and the strategy finds the right implementation

### Example

#### Strategy class

```js
class PaymentMethodStrategy {

  const customerInfoType = {
    country: string
    emailAddress: string
    name: string
    accountNumber?: number
    address?: string
    cardNumber?: number
    city?: string
    routingNumber?: number
    state?: string
  }
  
  static BankAccount(customerInfo: customerInfoType) {
    const { name, accountNumber, routingNumber } = customerInfo
    // do stuff to get payment
  }
  
  static BitCoin(customerInfo: customerInfoType) {
    const { emailAddress, accountNumber } = customerInfo
    // do stuff to get payment
  }
  
  static CreditCard(customerInfo: customerInfoType) {
    const { name, cardNumber, emailAddress } = customerInfo
    // do stuff to get payment
  }
  
  static MailIn(customerInfo: customerInfoType) {
    const { name, address, city, state, country } = customerInfo
    // do stuff to get payment
  }
  
  static PayPal(customerInfo: customerInfoType) {
    const { emailAddress } = customerInfo
    // do stuff to get payment
  }
}
```
#### Applying it

```js
const PaymentMethodStrategy = require('./PaymentMethodStrategy')
const config = require('./config')

class Checkout {
  constructor(strategy='CreditCard') {
    this.strategy = PaymentMethodStrategy[strategy]
  }
  
  // do some fancy code here and get user input and payment method
  
  changeStrategy(newStrategy) {
    this.strategy = PaymentMethodStrategy[newStrategy]
  }
  
  const userInput = {
    name: 'Malcolm',
    cardNumber: 3910000034581941,
    emailAddress: 'mac@gmailer.com',
    country: 'US'
  }
  
  const selectedStrategy = 'Bitcoin'
  
  changeStrategy(selectedStrategy)
  
  postPayment(userInput) {
    this.strategy(userInput)
  }
}

module.exports = new Checkout(config.paymentMethod.strategy)
```

## The Observer Design Pattern

The goal of the observer design pattern is to create a one-to-many relationship between the subject and all of the observers waiting for data so they can be updated. So anytime the state of the subject changes, all of the observers will be notified and updated instantly.

### Example

Say you have a single page application that has three feature dropdown lists that are dependent on the selection of a category from a higher level dropdown. This is common on many shopping sites, like Home Depot. You have a bunch of filters on the page that are dependent on the value of a top-level filter.

#### The top-level dropdown

```js
class CategoryDropdown {
  constructor() {
    this.categories = ['appliances', 'doors', 'tools']
    this.subscriber = []
  }
  
  // pretend there's some fancy code here
  
  subscribe(observer) {
    this.subscriber.push(observer)
  }
  
  onChange(selectedCategory) {
    this.subscriber.forEach(observer => observer.update(selectedCategory))
  }
}
```

#### The code for the other filters

```js
class FilterDropdown {
  constructor(filterType) {
    this.filterType = filterType
    this.items = []
  }
  
  // more fancy code here; maybe make that API call to get items list based on filterType
  
  update(category) {
    fetch('https://example.com')
      .then(res => this.items(res))
  }
}
```

#### Use of these files with the observer pattern

```js
const CategoryDropdown = require('./CategoryDropdown')
const FilterDropdown = require('./FilterDropdown')

const categoryDropdown = new CategoryDropdown() 

const colorsDropdown = new FilterDropdown('colors')
const priceDropdown = new FilterDropdown('price')
const brandDropdown = new FilterDropdown('brand')

categoryDropdown.subscribe(colorsDropdown)
categoryDropdown.subscribe(priceDropdown)
categoryDropdown.subscribe(brandDropdown)
```

## The Decorator Design Pattern

When using this pattern, you pass in a base class object to a decorator class constructor in order to construct a new decorated class.

### Example 

#### Customer class

```js
class Customer {
  constructor(balance=20) {
    this.balance = balance
    this.foodItems = []
  }
  
  buy(food) {
    if (food.price) < this.balance {
      console.log('you should get it')
      this.balance -= food.price
      this.foodItems.push(food)
    }
    else {
      console.log('maybe you should get something else')
    }
  }
}

module.exports = Customer
```

#### Sandwich class

```js
class Sandwich {
  constructor(type, price) {
    this.type = type
    this.price = price
  }
  
  order() {
    console.log(`You ordered a ${this.type} sandwich for $ ${this.price}.`)
  }
}

class DeluxeSandwich {
  constructor(baseSandwich) {
    this.type = `Deluxe ${baseSandwich.type}`
    this.price = baseSandwich.price + 1.75
  }
}

class ExquisiteSandwich {
  constructor(baseSandwich) {
    this.type = `Exquisite ${baseSandwich.type}`
    this.price = baseSandwich.price + 10.75
  }
  
  order() {
    console.log(`You ordered an ${this.type} sandwich. It's got everything you need to be happy for days.`)
  }
}

module.exports = { Sandwich, DeluxeSandwich, ExquisiteSandwich }
```

#### An example where this class is instantiated as if a customer was placing a sandwich order

```js
const { Sandwich, DeluxeSandwich, ExquisiteSandwich } = require('./Sandwich')
const Customer = require('./Customer')

const cust1 = new Customer(57)

const turkeySandwich = new Sandwich('Turkey', 6.49)
const bltSandwich = new Sandwich('BLT', 7.55)

const deluxeBltSandwich = new DeluxeSandwich(bltSandwich)
const exquisiteTurkeySandwich = new ExquisiteSandwich(turkeySandwich)

cust1.buy(turkeySandwich)
cust1.buy(bltSandwich)
```