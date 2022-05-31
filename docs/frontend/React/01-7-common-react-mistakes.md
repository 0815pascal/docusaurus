---
title: 7 common React mistakes
description: What they are and how to avoid them
---

# 7 common React mistakes

## 01 Not extracting component's logic in custom hooks

**Bad**:

```javascript
const ProductList = () => {
    const [products, setProducts] = React.useState([])

    React.useEffect(() => {
        const fetchProducts = async() => {
            const products = await ProductsApi.getProducts()

            setProducts(products)
        }

        fetchProducts()
    }, [])
    return (...)
}
```

**Good**:

```javascript
function useProducts(){
    const [products, setProducts] = React.useState([])

    React.useEffect(() => {
        const fetchProducts = async() => {
            const products = await ProductsApi.getProducts()
            setProducts(products)
        }
        fetchProducts()
    }, [])
    return products
}

const ProductList = () => {
    const products = useProducts()
    return (...)
}

const OtherComponentThatUsesProducts = () => {
    const products = useProducts()
    return (...)
}
```

## 02 Multiple partially matching routes

A common mistake is to have multiple routes with partially matching names. 

If we go to http://app.com/users/create, React Router will partially match `/users` route with `/users/create`, so it would incorrectly return the `Users` component. 

```javascript
<Route path="/users" component={Users} />
<Route path="/users/create" component={CreateUser} />
```

The exact param disables the partial matching for a route, and makes sure that it only returns the route if the path is an **exact** match to the current url. 

```javascript
<Route exact path="/users" component={Users} />
<Route path="/users/create" component={CreateUser} />
```

## 03 Mutating state directly

You should never mutate state directly as it might not cause a re-render if you update the state with the same reference. 

**Bad**:

```javascript
function MyComponent {
    const [fruits, setFruits] = React.useState([])

    const onAddFruit = (newFruit) => {
        fruits.push(newFruit)
        setFruits(fruits)
    }

    return (...)
}
```

**Good**: 

```javascript
function MyComponent {
    const [fruits, setFruits] = React.useState([])

    const onAddFruit = (newFruit) => {
        setFruits([...fruits, newFruit])
    }

    return (...)
```

## 04 Omitting functions in useEffect dependencies

**Bad:**

This is not ok, because fetchProduct won't re-run if productId changes

```javascript
function ProductgPage({productId}){
    const [product, setProduct] = useState(null);

    async function fetchProduct() {
        const response = await ProductsApi.getProductById(productId)
        const json = await response.json();
        setProduct(json);
    }

    useEffect(() => {
        fetchProduct();
    }, []);
}
```

**Good:**

Valid because our effect only uses productid

```javascript
function ProductgPage({productId}){
    const [product, setProduct] = useState(null);

    useEffect(() => {
        // By moving this function inside the effect, 
        // we can clearly see the values it uses. 
        async function fetchProduct() {
            const response = await ProductsApi.getProductById(productId)
            const json = await response.json();
            setProduct(json);
        }

        fetchProduct();
    }, [productId]);
```

## 05 Using state variable after setting its value

`useState` hook is asynchronous, so it doesn't guarantee the latest value of a state variable immediately. If you want to access the latest value of a state variable you can use `useEffect`. 

**Bad:**

```javascript
function MyComponent({productId}){
    const [mousePosition, setMousePosition] = useState({
        x:0,
        y:0
    })

    const onChangeMousePosition = (newPosition) => {
        setMousePosition({
            x: newPosition.x,
            y: newPosition.y    
        })

        if(mousePosition.x > 400){
            // do something
        }
    }
}
```

**Good:**

```javascript
function MyComponent({productId}){
    const [mousePosition, setMousePosition] = useState({
        x:0,
        y:0
    })

    const onChangeMousePosition = (newPosition) => {
        setMousePosition({
            x: newPosition.x,
            y: newPosition.y    
        })
    }

    useEffect(() => {
        if(mousePosition.x > 400){
            // do something
        }
    }, [mousePosition])
}
```

## Learning/using Redux too soon

Before using Redux you should:

- Feel comfortable with React and had tried using `useState`, `useReducer` or `context` to solve the problem you think Redux might solve.

- Understand the kind of application you're building, the kinds of problems that you need to solve and how Redux takes care of those problems. 

- Understand the tradeoffs involved in using Redux. 

You can find more information here: 

- https://redux.js.org/faq/general#when-should-i-learn-redux

- https://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/

- https://changelog.com/posts/when-and-when-not-to-reach-for-redux

- https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367

## Using `memo`/`useMemo` too soon

Before you apply optimizations like `memo` or `useMemo`, it might make sense to look if you can split the parts that change from the parts that don't change. 

**Bad:**

Changing the color would make the whole App component to re-render, resulting in a re-render of the `ExpensiveComponent`.

This can be solved by putting `React.useMemo()` inside the `ExpensiveComponent`, but first it's worth eploring other solutions. 

```javascript
export default function App() {
    let [color, setColor] = useState('red');
    return (
        <div>
            <input 
                value={color}
                onChange={(event) => {
                    setColor(event.target.value)
                }}
            />
            <p style={{ color }}>Hello, world!</p>
            <ExpensiveComponent />
        </div>
    );
}
```

**Good:**

```javascript
export default function App() {
    return (
        <ColorPicker>
            <p>Hello, world!</p>
            <ExpensiveComponent />
        </ColorPicker>
    );
}

function ColorPicker({ children }) {
    let [color, setColor] = useState("red");
    return (
        <div style={{ color }}
            <input 
                value={color}
                onChange={(event) => {
                    setColor(event.target.value)
                }}
            />
            {children}
        </div>
    );
}
```


