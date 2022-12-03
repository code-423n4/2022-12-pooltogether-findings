A pre-increment approach can be used instead of post-increment inside the `for loop` [here](https://github.com/pooltogether/ERC5164/blob/main/src/libraries/CallLib.sol#L61)
This will decrease the gas cost because `i++` is generally more expensive. It must increment a value and "return" the old value, so it may require holding two numbers in memory. 

<h2>Recommendation</h2>
Use `++i` instead of `i++`