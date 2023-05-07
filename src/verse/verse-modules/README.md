# Verse.org Modules

For more information about Verse.org modules, see the [Verse.org Modules](https://verse.org/modules) website.

## Random

The random module can be used to generate random numbers inside your code. The module contains three functions. The first is `GetRandomInt` which returns a random integer between the two numbers you pass in. The second is `GetRandomFloat` which returns a random float. These are inclusive, so if you call GetRandomInt(1,3), the response can be 1, 2 or 3. The third is `Shuffle` which takes an array and returns an array with the elements randomly reordered.

```ruby
# gives us access to the random module in our code
using { /Verse.org/Random }

# will give us a random number between 0 and 5 (inclusive)
RandomInt : int = GetRandomInt(0, 5)

# if we wanted to get a random value from the
Print("Randomly generated the number: {RandomInt}")

Numbers : []int = array{ 5, 4, 3, 2, 1 }

# numbers will be randomly reordered!
Numbers = Shuffle(Numbers)
for(Number : Numbers)
    Print("Number: {Number}")
```
