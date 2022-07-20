# CSE 11 SS1-2022 PA4 - Life of Cells
**Due date: Tuesday, July 26 @ 11:59PM PST**

**No slip day or late submission except for approved and legitimate cases.**

This is the final assignment of CSE 11 in SS1-2022 by Professor Cao at UCSD. 
## Provided Files
None

## Files to Submit
- Cell.java
- CellStationary.java
- CellMoveUp.java
- CellDivide.java
- CellMoveToggle.java
- CellMoveDiagonal.java
- CellMoveToggleChild.java
- PetriDish.java

## Goal
The goal of PA4 is to apply inheritance to defining the relationship among different types of cells (cell classes). In addition to creating these cell classes, you will also be creating a PetriDish class that represents a 2D board of different types of cells.

## Some Background
Conway's Game of Life is a cellular automaton that is simple to understand but has interesting properties. You can read more about it on [Wikipedia](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) but knowledge of it is not required for this assignment. 

In the original game, each cell of the board is equivalent. In our game, each cell of the board may be a different cell (in the biological sense) and may have different behaviors. 

We have abstracted out all biology, except for the term "apoptosis" which means "programmed cell death" (which we will use to mean that a cell disappears). We will also, at various points, use the word "spawn" to mean for a cell to come into existence. 

## Some General Notes
Unchanged from PA3. Follow these:
- Most of these notes are necessary for autograding purposes. If any of these does not make sense, you probably aren't doing anything that it applies to. However, you should make sure that when you do learn about it later on in this quarter that you follow these notes. We cannot be lenient regarding these things because everything is autograded to ensure fairness. 
- Make sure to read the autograder output after you submit to Gradescope (wait until the autograder is finished running). 
- Match the specifications that we provide _exactly_, otherwise we cannot ensure that the autograder will function correctly. This includes file names, class names, method signatures, extending, throwing, etc. 
- "Constants" should be defined as `private static final` variables. 
- Do not use _any_ static variables (other than for constants) or instance variables that are not specified in this writeup. We cannot ensure that these do not get clobbered during grading. Any extra variables used should be local only. 
- Unless otherwise specified, do not add any extra `import`s or use any packages not from `java.lang` other than those implied or explicitly named by this writeup. 
- Do not specify a package for your files. This will cause them to fail to compile with the autograder. 
- Do not add any extra classes to your files and do not write code in files that are not specified. 
- Do not call helper methods except from the class where they are implemented, as we will be using our own version of classes during grading (which will only have the instance variables and methods specified in this writeup). You shouldn't be able to call them anyway if you have declared them as `private`. 
- If a behavior (say, on some specific input) is not specified, you may handle that case however you want. However, if you implement a specific behavior for some special case of a required method, do not rely on that specific behavior when using that method as a helper method (only assume that the method works as specified in the writeup). 

<div style="page-break-after: always"></div>

## Part 1: Cells
```java 
public abstract class Cell {...}
public class CellStationary extends Cell {...}
public class CellMoveUp extends Cell {...}
public class CellDivide extends Cell {...}
public class CellMoveToggle extends CellMoveUp {...}
public class CellMoveDiagonal extends CellMoveUp {...}
public class CellMoveToggleChild extends CellMoveToggle {...}
```
![](https://i.imgur.com/X0tEdR0.png)

Since these classes are all public classes, they should be in separate `.java` files, where each file is named after the class. 

## Cell.java
```java
public abstract class Cell {...}
```
### TODO: Instance Variables for Cell
```java
public abstract class Cell {
    ...
    public int currRow;
    public int currCol;
    public int mass;
    ...
}
```
- `currRow` stores the row value of the cell. 
- `currCol` stores the column value of the cell. 
- `mass` stores the mass of the cell.
- Valid values for all the instance variables are ones that are not negative. 

### TODO: Methods to Implement for Cell
```java
public abstract class Cell {
    ...
    public Cell(int currRow, int currCol, int mass); 
    public Cell(Cell otherCell); 
    public void apoptosis(); 
    public int getCurrRow(); 
    public int getCurrCol(); 
    public int getMass(); 
    public boolean setCurrRow(int newRow); // optional
    public boolean setCurrCol(int newCol); // optional
    public boolean setMass(int newMass); // optional
    public void updatePosition(int[] newPosition); // optional
    public abstract boolean checkApoptosis(List<Cell> neighbors);
    ...
}
```

#### `public Cell(int currRow, int currCol, int mass)`
- This is the constructor for `Cell`. 
- Initialize all instance variables with the values passed in as arguments. 
- If an argument would make the appropriate instance variable invalid, set the appropriate instance variable to 0 instead. 
- Even though `Cell` is an abstract class, we need this constructor because all subclasses should use this constructor to initialize the instance variables inherited from `Cell`. 

#### `public Cell(Cell otherCell)`
- This is the copy constructor for `Cell`. 
- You can assume that `otherCell` is non-`null` (usually, if it were `null`, we would handle it by throwing a `NullPointerException`). 
- Initialize all instance variables with the instance variables of the `Cell` object passed in as an argument. 
- Even though `Cell` is an abstract class, we need this copy constructor because all subclasses should use this copy constructor to copy the instance variables inherited from `Cell`. 

#### `public void apoptosis()`
- This method will be called on a `Cell` when apoptosis happens. 
- Set `currRow`, `currCol`, and `mass` to `-1` to indicate cell death. 

#### Getters `getCurrRow()`, `getCurrCol()`, `getMass()`
- For each getter method, return the current value of the appropriate instance variable. 

#### (Optional) Setters `setCurrRow()`, `setCurrCol()`, `setMass()`
- For each (optional) setter method, set the appropriate instance variable if the new value is valid and return whether the new value was set or not. 

#### (Optional) "Setter" `updatePosition()`
- Set the `currRow` to the first (index 0) element of `newPosition` ands `currCol` to the second (index 1 ) element (following the validity rules of the other setters). 

#### `public abstract boolean checkApoptosis(List<Cell> neighbors)`
- Given a `List` of `Cell`s, determine if this cell should initiate apoptosis or not. Return `true` if the condition being checked for is satisfied and `false` otherwise (more details below). 
- This is an abstract method that each concrete subclass will implement with its own behavior. 

<div style="page-break-after: always"></div>

## Subclasses of Cell
```java
public class CellStationary extends Cell {...}
public class CellMoveUp extends Cell {...}
public class CellDivide extends Cell {...}
public class CellMoveToggle extends CellMoveUp {...}
public class CellMoveDiagonal extends CellMoveUp {...}
public class CellMoveToggleChild extends CellMoveToggle {...}
```
Each of these 6 classes derive from `Cell` and will have similar (but not the exact same) behaviors. 

### TODO: Methods to Implement for each Subclass
They will all implement the following methods with at least the following functionality (more details about special functionality will be described afterward, you will have to modify these signatures to be appropriate per class): 
```java
public class Subclass extends ParentClass { 
    ...
    public Subclass(int currRow, int currCol, int mass); 
    public Subclass(Subclass otherSubclass); 
    public String toString(); 
    public boolean checkApoptosis(List<Cell> neighbors); 
    ...
}
```
So each class will have two constructors (one taking the three `int` parameters in the order specified here and one being a copy constructor for the current class), override the `toString()` method, and override the `checkApoptosis()` method. You will need to adjust the method parameter types (specifically for the constructors) appropriately for each class. 

Note that even though some of these subclasses may have extra instance variables, the initial values of those are not passed into the constructor, so the first constructor should always only have those 3 parameters. Similarly, for a subclass with name `Subclass`, the copy constructor should take a parameter of type `Subclass`. 

#### `public Subclass(int currRow, int currCol, int mass)`
- This is the constructor for the subclass. 
- Invoke the parent class's constructor to initialize all instance variables with the values passed in as arguments. 

#### `public Subclass(Subclass otherSubclass)`
- This is the copy constructor for the subclass.
- You can assume that the argument is non-`null`
- Invoke the parent class's copy constructor to initialize all instance variables with the instance variables of the `otherSubclass` object passed in as an argument. 

#### `public String toString()`
- Return the `String` representation of the current object. Each class will have a different representation, but each representation will be a single character. 

#### `public boolean checkApoptosis(List<Cell> neighbors)`
- Return `true` or `false` based on `neighbors` depending on the conditions for apoptosis (returns `true` if the condition being "checked" for is true and `false` otherwise). Each class will have different conditions for apoptosis. Only non-`null` neighbors are included in this list. You can assume the argument is not `null`. 
- Note that this method does NOT call `apoptosis()`. This method is only for checking whether `apoptosis()` should be called later. 

<div style="page-break-after: always"></div>

## <span style="color:orange"><u>CellStationary Specifics</u></span>
### CellStationary `String` representation
- `"."`. 

### CellStationary conditions for `checkApoptosis`
- Checks for whether this cell has fewer than or equal to 7 and greater than or equal to 3 neighbors.

---

## <span style="color:orange"><u>CellMoveUp Specifics</u></span>
### CellMoveUp `String` representation
- `"^"`.

### CellMoveUp conditions for `checkApoptosis`
- Checks for whether this cell does not have exactly 4 neighbors.

---

## <span style="color:orange"><u>CellDivide Specifics</u></span>
### CellDivide `direction` instance variable
- `CellDivide` will have an extra instance variable, `public int direction`, that will represent the direction the cell will divide into. The variable is public for grading purposes.
- The (non-copy) constructor for this class should default `direction` to `1`. 

### CellDivide `String` representation
- `"+"`.

### CellDivide conditions for `checkApoptosis`
- Checks for whether this cell has exactly 3 neighboring cells.

---

## <span style="color:orange"><u>CellMoveToggle Specifics</u></span>
### CellMoveToggle `toggled` instance variable
- `CellMoveToggle` will have an extra instance variable, `public boolean toggled`, that will represent if the cell is currently "toggled" or not. It is made public for grading purposes.
- The (non-copy) constructor for this class should default `toggled` to `true`. 

### CellMoveToggle `String` representation
- `"T"` if "toggled" and `"t"` otherwise. 

### CellMoveToggle conditions for `checkApoptosis`
- Checks for whether this cell has fewer than 2 or greater than 5 neighbors.

---

## <span style="color:orange"><u>CellMoveDiagonal Specifics</u></span>
### CellMoveDiagonal `orientedRight` and `diagonalMoves` instance variables
- `CellMoveDiagonal` will have two extra instance variables, `public boolean orientedRight`, which will represent if the cell is currently oriented to the right or not (to the left), and `public int diagonalMoves`, which will count the number of moves this cell has made.
- The (non-copy) constructor for this class should default `orientedRight` to `true` and `diagonalMoves` to `0`.

### CellMoveDiagonal `String` representation
- `"/"` if oriented right and `"\"` otherwise. 

### CellMoveDiagonal conditions for `checkApoptosis`
- Checks for whether this cell has greater than 3 neighbors.

---

## <span style="color:orange"><u>CellMoveToggleChild Specifics</u></span>
### CellMoveToggleChild `numAlive` static variable
- `CellMoveToggleChild` will have an extra static variable, `public static int numAlive`, that will represent the number of instances of this cell type that are currently alive (so it should be `0` when there are no instances of this cell). 
- The constructor for this class should increment this value by `1` each time. This behavior should apply to both the normal constructor and the copy constructor.
- This class should also override the `apoptosis()` method to not only call its parent's `apoptosis()` method but to also decrement `numAlive` by `1`. 
- You can assume that this variable will always correctly track the number of alive `CellMoveToggleChild` objects assuming that your updates are working correctly. 

### CellMoveToggleChild `String` representation
- This class should not override `CellMoveToggle`'s `toString()` method. 

### CellMoveToggleChild conditions for `checkApoptosis`
- Checks for whether `CellMoveToggle`'s conditions for apoptosis are satisfied AND there are fewer than 10 `CellMoveToggleChild`s alive. 

<div style="page-break-after: always"></div>

## Part 2: PetriDish
```java
public class PetriDish {...}
```
A `PetriDish` object contains a board that holds all the cells growing on the petri dish. 
![](https://i.imgur.com/DuqftXa.png)





### TODO: Instance Variables for PetriDish
```java
public class PetriDish {
    ...
    public Cell[][] dish; 
    ...
}
```
- `dish` represents the petri dish in the current iteration. A `null` value in the array represents an "empty space" and a non-`null` value represents an "alive" cell. The board is represented with the 0th row at the top and the 0th column at the left (so the spot `dish[1][2]` is one row below and to the left of `dish[0][3]`). 
    
### TODO: Methods to Implement for PetriDish
```java
public class PetriDish {
    ...
    public PetriDish(String[][] board); 
    public String toString(); //optional
    ...
}
```

#### `public PetriDish(String[][] board)`
- This is the constructor of the `PetriDish` class. 
- `board` is a 2D array of Strings representing what cells the dish should be filled with. 
- Each String is the string `"null"` if that position in the petri dish is an "empty space" and is of the format `"{CELL_TYPE} {MASS}"` for "alive cells." 
- For example, the 2D String array could be created like this:
  `String[][] petri = new String[][]{ {"CellMoveUp 0", "CellMoveToggle 1", "CellMoveToggleChild 2", "null"}, {"CellMoveDiagonal 3", "CellDivide 4", "CellMoveToggle 5", "null"} };`
- Populate each position of the instance variable `dish` with references to unique objects corresponding to the specific types and masses denoted in the 2D String array. 
- Assume that `board` is always valid and only contains object types specified in this writeup (feel free to hardcode this). 

#### `public String toString()`
- This can be used to print `dish` to visualize this `PetriDish` (and is optional). 
- Below is an example implementation.
```java 
public String toString() { 
    StringBuilder sb = new StringBuilder();
    sb.append(HORIZONTAL_BARS); //typically 2*board.length+3 would display the board nicely
    for(int i = 0; i < board.length; i++){
        sb.append(VERTICAL_BAR);
        for(int j = 0; j < board[0].length; j++){
            sb.append(board[i][j] == null ? EMPTY_STRING : board[i][j].toString());
            sb.append(VERTICAL_BAR);
        }
        sb.append(NEW_LINE);
        sb.append(HORIZONTAL_BARS);
    }
    return sb.toString();
}
```

<div style="page-break-after: always"></div>

## PA4 Style
Coding style is an important part of ensuring readability and maintainability of your code. Namely, there are a few things you must have in each file/class/method:
1. File headers
2. Class headers
3. Method headers (note that javadoc allows for method headers to be inherited from a super method. So, it is often convention only to update the method header if something has changed enough that it seems necessary.
4. Inline comments
5. Proper indentation (do not intermingle spaces and tabs for indentation)
6. Descriptive variable names
7. No magic numbers (define any literal values other than the integral/numeric -1, 0, 1 as `private static final` values)
8. Reasonably short methods (if you have implemented each method according to specification in this write-up, you’re fine)
9. Lines shorter than 80 characters (note, tabs will be counted as 4 characters toward this limit. It is a good idea to set your tab width/size to be 4. A good way to check is using the command `grep -n '.\{81\}' *.java` in the directory with your files, but note that this won't necessarily take tabulation into account appropriately. Also note that trailing whitespaces contribute to this total.)
10. Javadoc conventions (@param, @return tags, /** header comments */, etc.)

A full style guide can be found [here](https://docs.google.com/document/d/1xqhafV9rKQXRO1G2hq9S_O5JqK-1xQw2Fpf9fPJO-sI). If you need any clarifications, feel free to ask on Edstem. 


## Submit PA4
### Turning in your code
Submit all the following files to Gradescope by **Tuesday, July 26 @ 11:59PM PST** 
- Cell.java
- CellStationary.java
- CellMoveUp.java
- CellDivide.java
- CellMoveToggle.java
- CellMoveDiagonal.java
- CellMoveToggleChild.java
- PetriDish.java

When submitting, please wait until the autograder finishes running and read the output. **Your code must compile in the autograder in order to receive proper partial credit.**
