# iebis_swdev_debugging
Source code to test debugging

## Instructions
First, **Fork** this project.

There are three exercises splitted in three branches of this repository. You must switch branches to checkout the code of each exercise.
Then, find the bugs that appear in each branch.
Fix the bugs if you can and answer to the questions proposed below.
Commit the code before checking out a different branch to avoid loosing the fixes that you have made to the code.

Once that you are done fixing bugs, **to score you must**:
1. Switch to the master branch.
2. Type below in this README.md file the answer to each question and paste some code that you have used to solve them.
3. Commit the changes
4. Push to your GitHub repository
5. **Finally place a Pull Request so I can see your proposed answers**


## Exercises
### Exercise 1
In this code there is a class called WordAnalyzer that contains several methods that analyzes some characteristics of the word passed as argument when the object WordAnalyzer is created.

For some reason, the methods are not working properly, sometimes they return the correct value and others don't. You need to answer the next questions.

#### Why the method _firstMultipleCharacter_ is returning "c" for the word _comprehensive_, when the correct answer should be "e"?
#### Why the method _firstRepeatedCharacter_ is throwing an exception?
#### Why the method _countGroupsRepeatedCharacters_ returns 3 in one case when it should be 4?

**Strategy**: Place breakpoints before the methods are executed, step into them and see what happens.


### Exercise 2
In this code we are placing mines in a board game where we have several spaces that can be mined. 
The boards can contain _Element_ objects, and since _Space_ and _Mine_ inherits from _Element_, the boards can contain this kind as well.

We have two boards of different size and place a different number of mines on each one. But in the second case it takes longer to place all the mines.

#### Why placing less bombs takes longer in the second case?
#### Knowing that usually there are going to be more bombs than spaces in the final boards, how would you change the method _minningTheBoard_ to be more efficient?

**Strategy**: Understand well what the code does. Use conditionals breakpoints.


### Exercise 3
In this case this code looks really simple. When the "d" reaches the value 1.0, the program should end, but it never does.

#### Why does not appear a message indicating that "d is 1"?
#### How will you fix it?

# Solutions
## Exercise 1
#### Why the method _firstMultipleCharacter_ is returning "c" for the word _comprehensive_, when the correct answer should be "e"?
Because the method *find()* is comparing every letter with itself, therefore returning every letter to find itself, since it only returns the first one, it gives out "c".
To fix we must add one to the position of the argument:
```java
private int find(char c, int pos)
    {
        for (int i = pos+1; i < word.length(); i++) //pos +1
        {
            if (word.charAt(i) == c)
            {
                return i;
            }
        }
        return -1;
    }
```
#### Why the method _firstRepeatedCharacter_ is throwing an exception?
This is because the method was comparing each letter to the one after, until the last. If we do every single letter but the last we get to stay within our range:
```java
 public char firstRepeatedCharacter()
    {
        for (int i = 0; i < word.length()-1; i++) // word.length()-1
        {
            char ch = word.charAt(i);
            if (ch == word.charAt(i + 1))
                return ch;
        }
        return 0;
    }
```
#### Why the method _countGroupsRepeatedCharacters_ returns 3 in one case when it should be 4?
This is because the array is starting at the second letter to avoid a null exception of -1. This can be fixed by adding an if statement and starting at i=0:
```java
 public int countGroupsRepeatedCharacters()
    {
        int c = 0;
        for (int i = 0; i < word.length() - 1; i++) // 1 = 0 
        {
            if (word.charAt(i) == word.charAt(i + 1)) // found a repetition
            {
                if (i != 0) {                           //checking if its the first letter
                    
                
                if (word.charAt(i - 1) != word.charAt(i)) // it't the start
                    c++;
                }
                else{
                    c++;
                }
            }
        }
        return c;
    }
```

## Exercise 2
In this exercise we are messing with 2 different *boards* (hashMaps) where the first has 2,000,000 size and the second 1,500,000 size. Since the amount of bombs in both case is either 1,499,999 or 1,500,000, this represents a bigger portion of the Blue board (second) therefore it is innificient to fill it up choosing random numbers. This is why it would be more efficient if the filling up of the board was already done with the mines and the mining process becomes the empty spaces of the board. This can be done by changing some of the code as follows:
#### from
```java
public static void settingTheBoard(int size) {
        // Setting the board
        myBoard = new HashMap<>();

        for(int i = 0; i < size; i++) {
            myBoard.put(i, new Space());
        }
    }

    public static void minningTheBoard(int numberMines) {
        Random random = new Random();
        while (numberMines > 0) {
            Integer trial = new Integer(random.nextInt(myBoard.size()));

            if (myBoard.get(trial) instanceof Space) {
                myBoard.put(trial, new Mine());
                numberMines--;
            }
        }
    }
```
#### to
```java
public static void settingTheBoard(int size) {
        // Setting the board
        myBoard = new HashMap<>();

        for(int i = 0; i < size; i++) {
            myBoard.put(i, new Mine());
        }
    }

    public static void minningTheBoard(int numberMines) {
        numberMines = myBoard.size() - numberMines;
        Random random = new Random();
        while (numberMines > 0) {
            Integer trial = new Integer(random.nextInt(myBoard.size()));

            if (myBoard.get(trial) instanceof Mine) {
                myBoard.put(trial, new Space());
                numberMines--;
            }
        }
    }
```
this way the roles of the functions is switched but the variables names continue the same.

## Exercise 3
The problem here is how doubles work in java. Therefore the double representation of 1 = 0.9999999999999... 
One way of tackling he issue was instead of checking if it is the same exact value, I checked if the difference between it was extremely small, which it was and it worked:
```java
public class Main {

    public static void main(String [] args) {
        double d = 0.0;

        while (d - 1.0 < 0.0000001) {
            d += 0.10;
        }

        System.out.println("d is 1");
    }
}
```