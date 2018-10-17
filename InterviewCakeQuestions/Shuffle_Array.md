Ref:- https://www.interviewcake.com/question/java/shuffle?utm_source=weekly_email&utm_source=drip&utm_campaign=weekly_email&utm_campaign=Interview%20Cake%20Weekly%20Problem%20%23213:%20In-Place%20Shuffle&utm_medium=email&utm_medium=email

Write a method for doing an in-place ↴
An in-place algorithm operates directly on its input and changes it, instead of creating and returning a new object. This is sometimes called destructive, since the original input is "destroyed" when it's edited to create the new output.

Careful: "In-place" does not mean "without creating any additional variables!" Rather, it means "without creating a new copy of the input." In general, an in-place method will only create additional variables that are O(1)O(1) space.

Here are two methods that do the same operation, except one is in-place and the other is out-of-place:

  public static int[] squareArrayInPlace(int[] intArray) {

    for (int i = 0; i < intArray.length; i++) {
        intArray[i] *= intArray[i];
    }

    // NOTE: we could make this method void, since we modify
    // intArray in place.
    return intArray;
}

public static int[] squareArrayOutOfPlace(int[] intArray) {

    // we allocate a new array with the length of the input array
    int[] squaredArray = new int[intArray.length];

    for (int i = 0; i < intArray.length; i++) {
        squaredArray[i] = (int) Math.pow(intArray[i], 2);
    }

    return squaredArray;
}

Working in-place is a good way to save space. An in-place algorithm will generally have O(1)O(1) space cost.

But be careful: an in-place algorithm can cause side effects. Your input is "destroyed" or "altered," which can affect code outside of your method. For example:

  int[] originalArray = new int[]{2, 3, 4, 5};
int[] squaredArray = squareArrayInPlace(originalArray);

System.out.println("squared: " + Arrays.toString(squaredArray));
// prints: squared: [4, 9, 16, 25]

System.out.println("original array: " + Arrays.toString(originalArray));
// prints: original array: [4, 9, 16, 25], confusingly!

// and if squareArrayInPlace() didn't return anything,
// which it could reasonably do, squaredArray would be null!

Generally, out-of-place algorithms are considered safer because they avoid side effects. You should only use an in-place algorithm if you're very space constrained or you're positive you don't need the original input anymore, even for debugging.

shuffle of an array.

The shuffle must be "uniform," meaning each item in the original array must have the same probability of ending up in each spot in the final array.

Assume that you have a method getRandom(floor, ceiling) for getting a random integer that is >= floor and <= ceiling.

Gotchas
A common first idea is to walk through the array and swap each element with a random other element. Like so:

  import java.util.Random;

private static Random rand = new Random();

private static int getRandom(int floor, int ceiling) {
    return rand.nextInt((ceiling - floor) + 1) + floor;
}

public static void naiveShuffle(int[] theArray) {

    // for each index in the array
    for (int firstIndex = 0; firstIndex < theArray.length; firstIndex++) {

        // grab a random other index
        int secondIndex = getRandom(0, theArray.length - 1);

        // and swap the values
        if (secondIndex != firstIndex) {
            int temp = theArray[firstIndex];
            theArray[firstIndex] = theArray[secondIndex];
            theArray[secondIndex] = temp;
        }
    }
}

However, this does not give a uniform random distribution.

Why? We could calculate the exact probabilities of two outcomes to show they aren't the same. But the math gets a little messy. Instead, think of it this way:

Suppose our array had 33 elements: [a, b, c]. This means it'll make 33 calls to getRandom(0, 2). That's 33 random choices, each with 33 possibilities. So our total number of possible sets of choices is 3*3*3=273∗3∗3=27. Each of these 2727 sets of choices is equally probable.

But how many possible outcomes do we have? If you paid attention in stats class you might know the answer is 3!3!, which is 66. Or you can just list them by hand and count:

  a, b, c
a, c, b
b, a, c
b, c, a
c, b, a
c, a, b
But our method has 2727 equally-probable sets of choices. 2727 is not evenly divisible by 66. So some of our 66 possible outcomes will be achievable with more sets of choices than others.

We can do this in a single pass. O(n)O(n) time and O(1)O(1) space.

A common mistake is to have a mostly-uniform shuffle where an item is less likely to stay where it started than it is to end up in any given slot. Each item should have the same probability of ending up in each spot, including the spot where it starts.

Breakdown
It helps to start by ignoring the in-place ↴ requirement, then adapt the approach to work in place.

Also, the name "shuffle" can be slightly misleading—the point is to arrive at a random ordering of the items from the original array. Don't fixate too much on preconceived notions of how you would "shuffle" e.g. a deck of cards.

How might we do this by hand?

We can simply choose a random item to be the first item in the resulting array, then choose another random item (from the items remaining) to be the second item in the resulting array, etc.

Assuming these choices were in fact random, this would give us a uniform shuffle. To prove it rigorously, we can show any given item aa has the same probability (\frac{1}{n}
​n
​
​1
​​ ) of ending up in any given spot.

First, some stats review: to get the probability of an outcome, you need to multiply the probabilities of all the steps required for that outcome. Like so:

Outcome	Steps	Probability
item #1 is a	a is picked first	\frac{1}{n}
​n
​
​1
​​ 
item #2 is a	a not picked first, a picked second	\frac{(n-1)}{n} * \frac{1}{(n-1)} =
​n
​
​(n−1)
​​ ∗
​(n−1)
​
​1
​​ = \frac{1}{n}
​n
​
​1
​​ 
item #3 is a	a not picked first, a not picked second, a picked third	\frac{(n-1)}{n} * \frac{(n-2)}{(n-1)} * \frac{1}{(n-2)} =
​n
​
​(n−1)
​​ ∗
​(n−1)
​
​(n−2)
​​ ∗
​(n−2)
​
​1
​​ = \frac{1}{n}
​n
​
​1
​​ 
item #4 is a	a not picked first, a not picked second, a not picked third, a picked fourth	\frac{(n-1)}{n} * \frac{(n-2)}{(n-1)} * \frac{(n-3)}{(n-2)} * \frac{1}{(n-3)} =
​n
​
​(n−1)
​​ ∗
​(n−1)
​
​(n−2)
​​ ∗
​(n−2)
​
​(n−3)
​​ ∗
​(n−3)
​
​1
​​ = \frac{1}{n}
​n
​
​1
​​ 
So, how do we implement this in code?

If we didn't have the "in-place" requirement, we could allocate a new array, then one-by-one take a random item from the input array, remove it, put it in the first position in the new array, and keep going until the input array is empty (well, probably a copy of the input array—best not to destroy the input)

How can we adapt this to be in place?

What if we make our new "random" array simply be the front of our input array?

Solution
We choose a random item to move to the first index, then we choose a random other item to move to the second index, etc. We "place" an item in an index by swapping it with the item currently at that index.

Crucially, once an item is placed at an index it can't be moved. So for the first index, we choose from nn items, for the second index we choose from n-1n−1 items, etc.

  import java.util.Random;

private static Random rand = new Random();

private static int getRandom(int floor, int ceiling) {
    return rand.nextInt((ceiling - floor) + 1) + floor;
}

public static void shuffle(int[] theArray) {

    // if it's 1 or 0 items, just return
    if (theArray.length <= 1) {
        return;
    }

    // walk through from beginning to end
    for (int indexWeAreChoosingFor = 0;
        indexWeAreChoosingFor < theArray.length - 1; indexWeAreChoosingFor++) {

        // choose a random not-yet-placed item to place there
        // (could also be the item currently in that spot)
        // must be an item AFTER the current item, because the stuff
        // before has all already been placed
        int randomChoiceIndex = getRandom(indexWeAreChoosingFor, theArray.length - 1);

        // place our random choice in the spot by swapping
        if (randomChoiceIndex != indexWeAreChoosingFor) {
            int valueAtIndexWeChoseFor = theArray[indexWeAreChoosingFor];
            theArray[indexWeAreChoosingFor] = theArray[randomChoiceIndex];
            theArray[randomChoiceIndex] = valueAtIndexWeChoseFor;
        }
    }
}

This is a semi-famous algorithm known as the Fisher-Yates shuffle (sometimes called the Knuth shuffle).

Complexity
O(n)O(n) time and O(1)O(1) space.

What We Learned
Don't worry, most interviewers won't expect a candidate to know the Fisher-Yates shuffle algorithm. Instead, they'll be looking for the problem-solving skills to derive the algorithm, perhaps with a couple hints along the way.

They may also be looking for an understanding of why the naive solution is non-uniform (some outcomes are more likely than others). If you had trouble with that part, try walking through it again.
