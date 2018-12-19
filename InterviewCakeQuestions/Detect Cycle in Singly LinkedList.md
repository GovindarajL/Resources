Ref:- https://www.interviewcake.com/question/java/linked-list-cycles?utm_source=weekly_email&utm_source=drip&utm_campaign=weekly_email&utm_campaign=Interview%20Cake%20Weekly%20Problem%20%23222:%20Does%20This%20Linked%20List%20Have%20A%20Cycle%3F&utm_medium=email&utm_medium=email

You have a singly-linked list ↴ and want to check if it contains a cycle.

A singly-linked list is built with nodes, where each node has:

node.next—the next node in the list.
node.value—the data held in the node. For example, if our linked list stores people in line at the movies, node.value might be the person's name.
For example:

  public class LinkedListNode {

    public int value;
    public LinkedListNode next;

    public LinkedListNode(int value) {
        this.value = value;
    }
}

A cycle occurs when a node’s next points back to a previous node in the list. The linked list is no longer linear with a beginning and end—instead, it cycles through a loop of nodes.

Write a method containsCycle() that takes the first node in a singly-linked list and returns a boolean indicating whether the list contains a cycle.

Gotchas
Careful—a cycle can occur in the middle of a list, or it can simply mean the last node links back to the first node. Does your method work for both?

We can do this in O(n)O(n) time and O(1)O(1) space!

Breakdown
Because a cycle could result from the last node linking to the first node, we might need to look at every node before we even see the start of our cycle again. So it seems like we can’t do better than O(n)O(n) runtime.

How can we track the nodes we’ve already seen?

Using a hash set, we could store all the nodes we’ve seen so far. The algorithm is simple:

If the current node is already in our hash set, we have a cycle. Return true.
If the current node is null we've hit the end of the list. Return false.
Else throw the current node in our hash set and keep going.
What are the time and space costs of this approach? Can we do better?

Our runtime is O(n)O(n), the best we can do. But our space cost is also O(n)O(n). Can we get our space cost down to O(1)O(1) by storing a constant number of nodes?

Think about a looping list and a linear list. What happens when you traverse one versus the other?

A linear list has an end—a node that doesn’t have a next node. But a looped list will run forever. We know we don’t have a loop if we ever reach an end node, but how can we tell if we’ve run into a loop?

We can’t just run our method for a really long time, because we’d never really know with certainty if we were in a loop or just a really long list.

Imagine that you're running on a long, mountainous running trail that happens to be a loop. What are some ways you can tell you're running in a loop?

One way is to look for landmarks. You could remember one specific point, and if you pass that point again, you know you’re running in a loop. Can we use that principle here?

Well, our cycle can occur after a non-cyclical "head" section in the beginning of our linked list. So we'd need to make sure we chose a "landmark" node that is in the cyclical "tail" and not in the non-cyclical "head." That seems impossible unless we already know whether or not there's a cycle...

Think back to the running trail. Besides landmarks, what are some other ways you could tell you’re running in a loop? What if you had another runner? (Remember, it’s a singly-linked list, so no running backwards!)

A tempting approach could be to have the other runner stop and act as a "landmark," and see if you pass her again. But we still have the problem of making sure our "landmark" is in the loop and not in the non-looping beginning of the trail.

What if our "landmark" runner moves continuously but slowly?

If we sprint quickly down the trail and the landmark runner jogs slowly, we will eventually "lap" (catch up to) the landmark runner!

But what if there isn't a loop?

Then we (the faster runner) will simply hit the end of the trail (or linked list).

So let's make two variables, slowRunner and fastRunner. We’ll start both on the first node, and every time slowRunner advances one node, we’ll have fastRunner advance two nodes.

If fastRunner catches up with slowRunner, we know we have a loop. If not, eventually fastRunner will hit the end of the linked list and we'll know we don't have a loop.

This is a complete solution! Can you code it up?

Make sure the method eventually terminates in all cases!

Solution
We keep two pointers to nodes (we'll call these “runners”), both starting at the first node. Every time slowRunner moves one node ahead, fastRunner moves two nodes ahead.

If the linked list has a cycle, fastRunner will "lap" (catch up with) slowRunner, and they will momentarily equal each other.

If the list does not have a cycle, fastRunner will reach the end.

  public static boolean containsCycle(LinkedListNode firstNode) {

    // start both runners at the beginning
    LinkedListNode slowRunner = firstNode;
    LinkedListNode fastRunner = firstNode;

    // until we hit the end of the list
    while (fastRunner != null && fastRunner.next != null) {
        slowRunner = slowRunner.next;
        fastRunner = fastRunner.next.next;

        // case: fastRunner is about to "lap" slowRunner
        if (fastRunner == slowRunner) {
            return true;
        }
    }

    // case: fastRunner hit the end of the list
    return false;
}

Complexity
O(n)O(n) time and O(1)O(1) space.

The runtime analysis is a little tricky. The worst case is when we do have a cycle, so we don't return until fastRunner equals slowRunner. But how long will that take?

First, we notice that when both runners are circling around the cycle fastRunner can never skip over slowRunner. Why is this true?

Suppose fastRunner had just skipped over slowRunner. fastRunner would only be 1 node ahead of slowRunner, since their speeds differ by only 1. So we would have something like this:

  [ ] -> [s] -> [f]
What would the step right before this "skipping step" look like? fastRunner would be 2 nodes back, and slowRunner would be 1 node back. But wait, that means they would be at the same node! So fastRunner didn't skip over slowRunner! (This is a proof by contradiction.)

Since fastRunner can't skip over slowRunner, at most slowRunner will run around the cycle once and fastRunner will run around twice. This gives us a runtime of O(n)O(n).

For space, we store two variables no matter how long the linked list is, which gives us a space cost of O(1)O(1).

Bonus
How would you detect the first node in the cycle? Define the first node of the cycle as the one closest to the head of the list.
Would the program always work if the fast runner moves three steps every time the slow runner moves one step?
What if instead of a simple linked list, you had a structure where each node could have several "next" nodes? This data structure is called a "directed graph." How would you test if your directed graph had a cycle?
What We Learned
Some people have trouble coming up with the "two runners" approach. That's expected—it's somewhat of a blind insight. Even great candidates might need a few hints to get all the way there. And that's fine.

Remember that the coding interview is a dialogue, and sometimes your interviewer expects she'll have to offer some hints along the way.

One of the most impressive things you can do as a candidate is listen to a hint, fully understand it, and take it to its next logical step. Interview Cake gives you lots of opportunities to practice this. Don't be shy about showing lots of hints on our exercises—that's what they're there for!
