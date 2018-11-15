Ref:- https://www.interviewcake.com/question/java/bracket-validator?utm_source=weekly_email&utm_source=drip&utm_campaign=weekly_email&utm_campaign=Interview%20Cake%20Weekly%20Problem%20%23217:%20Bracket%20Validator&utm_medium=email&utm_medium=email

You're working with an intern that keeps coming to you with JavaScript code that won't run because the braces, brackets, and parentheses are off. To save you both some time, you decide to write a braces/brackets/parentheses validator.

Let's say:

'(', '{', '[' are called "openers."
')', '}', ']' are called "closers."
Write an efficient method that tells us whether or not an input string's openers and closers are properly nested.

Examples:

"{ [ ] ( ) }" should return true
"{ [ ( ] ) }" should return false
"{ [ }" should return false
Gotchas
Simply making sure each opener has a corresponding closer is not enough—we must also confirm that they are correctly ordered.

For example, "{ [ ( ] ) }" should return false, even though each opener can be matched to a closer.

We can do this in O(n)O(n) time and space. One iteration is all we need!

Breakdown
We can use a greedy ↴ approach to walk through our string character by character, making sure the string validates "so far" until we reach the end.

What do we do when we find an opener or closer?

Well, we'll need to keep track of our openers so that we can confirm they get closed properly. What data structure should we use to store them? When choosing a data structure, we should start by deciding on the properties we want. In this case, we should figure out how we will want to retrieve our openers from the data structure! So next we need to know: what will we do when we find a closer?

Suppose we're in the middle of walking through our string, and we find our first closer:

  [ { ( ) ] . . . .
      ^
How do we know whether or not that closer in that position is valid?

A closer is valid if and only if it's the closer for the most recently seen, unclosed opener. In this case, '(' was seen most recently, so we know our closing ')' is valid.

So we want to store our openers in such a way that we can get the most recently added one quickly, and we can remove the most recently added one quickly (when it gets closed). Does this sound familiar?

What we need is a stack! ↴
Worst Case
space	O(n)O(n)
push	O(1)O(1)
pop	O(1)O(1)
peek	O(1)O(1)
A stack stores items in a last-in, first-out (LIFO) order.

Picture a pile of dirty plates in your sink. As you add more plates, you bury the old ones further down. When you take a plate off the top to wash it, you're taking the last plate you put in. "Last in, first out."


Strengths:
Fast operations. All stack operations take O(1)O(1) time.
Uses:
The call stack is a stack that tracks function calls in a program. When a function returns, which function do we "pop" back to? The last one that "pushed" a function call.
Depth-first search uses a stack (sometimes the call stack) to keep track of which nodes to visit next.
String parsing—stacks turn out to be useful for several types of string parsing.
Implementation
You can implement a stack with either a linked list or a dynamic array—they both work pretty well:

Stack Push	Stack Pop
Linked Lists	insert at head	remove at head
Dynamic Arrays	append	remove last element
Java comes with a built in stack implementation , but it's better to use Deques instead.

The stack implementaton has a few big drawbacks: it doesn't have an interface and it extends the Vector class, which has thread synchronization overhead.


Solution
We iterate through our string, making sure that:

each closer corresponds to the most recently seen, unclosed opener
every opener and closer is in a pair
We use a stack ↴ to keep track of the most recently seen, unclosed opener. And if the stack is ever empty when we come to a closer, we know that closer doesn't have an opener.

So as we iterate:

If we see an opener, we push it onto the stack.
If we see a closer, we check to see if it is the closer for the opener at the top of the stack. If it is, we pop from the stack. If it isn't, or if the stack is empty, we return false.
If we finish iterating and our stack is empty, we know every opener was properly closed.

  import java.util.ArrayDeque;
import java.util.Deque;
import java.util.HashSet;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

public static boolean isValid(String code) {

    Map<Character, Character> openersToClosers = new HashMap<>();
    openersToClosers.put('(', ')');
    openersToClosers.put('[', ']');
    openersToClosers.put('{', '}');

    Set<Character> openers = openersToClosers.keySet();
    Set<Character> closers = new HashSet<>(openersToClosers.values());

    Deque<Character> openersStack = new ArrayDeque<>();

    for (char c : code.toCharArray()) {
        if (openers.contains(c)) {
            openersStack.push(c);
        } else if (closers.contains(c)) {
            if (openersStack.isEmpty()) {
                return false;
            } else {
                char lastUnclosedOpener = openersStack.pop();

                // if this closer doesn't correspond to the most recently
                // seen unclosed opener, short-circuit, returning false
                if (openersToClosers.get(lastUnclosedOpener) != c) {
                    return false;
                }
            }
        }
    }
    return openersStack.isEmpty();
}

Complexity
O(n)O(n) time (one iteration through the string), and O(n)O(n) space (in the worst case, all of our characters are openers, so we push them all onto the stack).

Bonus
In Ruby, sometimes expressions are surrounded by vertical bars, "|like this|". Extend your validator to validate vertical bars. Careful: there's no difference between the "opener" and "closer" in this case—they're the same character!

What We Learned
The trick was to use a stack. ↴

It might have been difficult to have that insight, because you might not use stacks that much.

Two common uses for stacks are:

parsing (like in this problem)
tree or graph traversal (like depth-first traversal)
So remember, if you're doing either of those things, try using a stack!
