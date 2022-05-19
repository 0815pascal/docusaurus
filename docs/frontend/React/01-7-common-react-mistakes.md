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

```javascript title="Fruits array reference hasn't changed"
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

```javascript title="Here we are passing in a new array to setFruits." 
function MyComponent {
    const [fruits, setFruits] = React.useState([])

    const onAddFruit = (newFruit) => {
        setFruits([...fruits, newFruit])
    }

    return (...)
```