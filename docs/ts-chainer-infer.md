# Ts Chainer Infer

```ts
function myChainer<T>(args: T) {
  return {
    map: <R>(innerFuction: (payload: T) => R) => {
      return myChainer(innerFuction(args));
    },
  };
}
```

````ts
const result = myChainer(0)
    .map(arg => {
      // ^?
      return {
        x: arg
      }
    })
    .map((arg)=> {
      // ^?
       return {
        y:1,
        ...arg
       }
    })
    .map(arg =>  {
      // ^?
      arg.x
    })
    ```
````
