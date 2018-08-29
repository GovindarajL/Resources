Aug 29 2018
```
1. To exclude the specific ranges of indexes from an array of elements.  
    A naive approach is to delete the element using the extra space.
    for(int i=0;i<len;i++){
    if(i<=L  i>=R)
     push arr[i] to some new arraylist
     }
     Time Complexity: O(n) Space Complexity: O(n)
     
     An efficient solution is without using the extra space
     int j=0;
     for(int i=0;i<len;i++){
     if(i<=L || i>=R){
      arr[j]=arr[i];
      j++;
      }
      }
      return j; //return the size of the array
      
      Ref: https://www.geeksforgeeks.org/delete-array-element-in-given-index-range-l-r/```
      
2. 
