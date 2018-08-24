Ref:- https://www.interviewcake.com/question/java/reverse-linked-list?utm_source=weekly_email&utm_source=drip&utm_campaign=weekly_email&utm_campaign=Interview%20Cake%20Weekly%20Problem%20%23205:%20Reverse%20A%20Linked%20List&utm_medium=email&utm_medium=email

Hooray! It's opposite day. Linked lists go the opposite way today.

Write a method for reversing a linked list. ↴ Do it in place. ↴

Your method will have one input: the head of the list.

Your method should return the new head of the list.

Here's a sample linked list node class:

  public class LinkedListNode {

    public int value;
    public LinkedListNode next;

    public LinkedListNode(int value) {
        this.value = value;
    }
}

Gotchas
We can do this in O(1)O(1) space. So don't make a new list; use the existing list nodes!

We can do this is in O(n)O(n) time.

Careful—even the right approach will fail if done in the wrong order.

Try drawing a picture of a small linked list and running your method by hand. Does it actually work?

The most obvious edge cases are:

the list has 0 elements
the list has 1 element
Does your method correctly handle those cases?

Breakdown
Our first thought might be to build our reversed list "from the beginning," starting with the head of the final reversed linked list.

The head of the reversed list will be the tail of the input list. To get to that node we'll have to walk through the whole list once (O(n)O(n) time). And that's just to get started.

That seems inefficient. Can we reverse the list while making just one walk from head to tail of the input list?

We can reverse the list by changing the next pointer of each node. Where should each node's next pointer...point?

Each node's next pointer should point to the previous node.

How can we move each node's next pointer to its previous node in one pass from head to tail of our current list?

Solution
In one pass from head to tail of our input list, we point each node's next pointer to the previous item.

The order of operations is important here! We're careful to copy currentNode.next into next before setting currentNode.next to previousNode. Otherwise "stepping forward" at the end could actually mean stepping back to previousNode!

  public static LinkedListNode reverse(LinkedListNode headOfList) {
    LinkedListNode currentNode = headOfList;
    LinkedListNode previousNode = null;
    LinkedListNode nextNode = null;

    // until we have 'fallen off' the end of the list
    while (currentNode != null) {

        // copy a pointer to the next element
        // before we overwrite currentNode.next
        nextNode = currentNode.next;

        // reverse the 'next' pointer
        currentNode.next = previousNode;

        // step forward in the list
        previousNode = currentNode;
        currentNode = nextNode;
    }

    return previousNode;
}

We return previousNode because when we exit the list, currentNode is null. Which means that the last node we visited—previousNode—was the tail of the original list, and thus the head of our reversed list.

Complexity
O(n)O(n) time and O(1)O(1) space. We pass over the list only once, and maintain a constant number of variables in memory.

Bonus
This in-place ↴ reversal destroys the input linked list. What if we wanted to keep a copy of the original linked list? Write a method for reversing a linked list out-of-place.

What We Learned
It's one of those problems where, even once you know the procedure, it's hard to write a bug-free solution. Drawing it out helps a lot. Write out a sample linked list and walk through your code by hand, step by step, running each operation on your sample input to see if the final output is what you expect. This is a great strategy for any coding interview question.
