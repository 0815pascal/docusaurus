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