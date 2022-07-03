# Message Search
Message Search Problem for Artificial Intelligence, implemented with `python 3.10`

You can find full documentation in romanian [(here)](../main/Documentation.pdf) :heart: :smile:

# Contents:
- [Context of the problem](#context-of-the-problem)
- [States, transitions and cost](#states-transitions-and-cost)
- [Files structure:](#file-structure)
  * [Input files:](#input-files)
    * [Example of input](#example-of-input)
    * [Types of inputs tested](#types-of-inputs-tested)
- [How to run this project:](#how-to-run-this-project)
  * [Example of running this project](#example-of-running-this-project)
- [Heuristics:](#heuristics)
  * [Trivial heuristic](#trivial-heuristic)
  * [Admissible heuristic 1](#admissible-heuristic-1)
  * [Admissible heuristic 2](#admissible-heuristic-2)
  * [Non-dmissible heuristic](#non-dmissible-heuristic)
- [Validations and optimizations:](#validations-and-optimizations)
  * [Validations](#validations)
  * [Optimizations](#optimizations)
- [Algorithm Comparison:](#algorithm-comparison)
  * [for input3](#for-input3)
  * [for input4](#for-input4-with-timeout-set-default-100s)
- [Conclusion](#conclusion)

## Context of the problem

On the way to school, a child heard two older children talking about a colleague and a good friend of his. The two wanted to make a mean prank on him in the first break. But the baby arrived after school had started and had to sit directly on the bench without being able to warn his colleague of the danger. Fortunately, it occurred to him to send him from colleague to colleague a note with a message warning him about the prank.

The children are seated in benches by two people each. The banks are arranged in K columns as follows (example for K = 3):

![photo](https://user-images.githubusercontent.com/73616883/177019654-c5a493a0-db5d-443d-8a1d-30b6c383c604.png)

A child can easily give the ticket to the bank colleague, just as unnoticed by the teacher, he can give the ticket to the colleagues in front or behind him, but not diagonally, because lying on the bench would catch the teacher's eye.

Considering the class excerpt below:

![photo2](https://user-images.githubusercontent.com/73616883/177019656-e9fb35e5-51da-436b-bfbe-649597eafb27.png)

Student _**a**_ can send the ticket only to _**b**_ or _**c**_.

Problems arise from the fact that some seats in banks can be vacant, but also from the fact that a number of colleagues are upset with each other and do not talk to each other, so they would not even send the ticket (the upset is reciprocal).
Also, passing the ticket from one row to another is more difficult, because it can be seen very easily by the teacher, so the only banks between which the transfer can be made are the penultimate and last on each row.

In addition, the teacher has a list of students that he listens to that day. The children are listened to in class in the order in the list. An obedience lasts as long as M ticket moves. When the teacher listens to a student, he is next to him, so the ticket is not allowed to be next to that child (neither in his bench, or in the front or back, nor in the benches in the neighboring columns (which are at the same position with the student's bench listened to, either with a forward position or a backward position.

The child wants to write on the ticket the path he has to take, from one colleague to another, to be sure that he does not get lost in class and does not reach his friend until the beginning of the break.

We consider by convetion that:
- each child is uniquely identified by his or her name.
- no child is called 'upset' or 'free'.
- free seats are marked with the identifier 'free'.

## States, transitions and cost

A state is given by the position of the ticket and who is being listened to by the teacher at that moment. If the ticket has not reached its destination and the teacher has finished listening, it means that there will be no more movement restrictions related to this aspect. The cost of a ticket move to the same bank is 0, between consecutive banks is 1 and between columns of banks is 2. Also if the ticket does not move, cost is 0.

```Python
def calculeaza_cost(self, nodCurent, lin_nou, col_nou):
    '''
    :param nodCurent: nodul, care contine linia si coloana unde se afla biletul actual
    :param lin_nou: linia pe care se muta biletul
    :param col_nou: coloana pe care se muta biletul
    :return: costul mutarii
    '''
    if nodCurent.pozitie_bilet == (lin_nou, col_nou):  # daca ramane pe loc
        return 0
    elif nodCurent.pozitie_bilet[0] != lin_nou:  # mutare pe coloana sus-jos
        return 1
    else:
        if nodCurent.pozitie_bilet[1] % 2 == 0:  # sunt pe un scaun stang in banca
            if col_nou == nodCurent.pozitie_bilet[1] + 1:  # mut in aceeasi banca la dreapta
                return 0
            else:  # mut pe alta coloana in stanga
                return 2
        else:  # sunt pe un scaun drept in banca
            if col_nou == nodCurent.pozitie_bilet[1] - 1:  # mut in aceeasi banca la stanga
                return 0
            else:  # mut pe alta coloana la dreapta
                return 2
```

## File structure
We have a folder called 'folder_input' which contains the input files and folder called 'folder_output' which will contain for each input file, the right output.
### Input files
The format of the input file is as follows. The first lines of the file specify the placement in the benches. Each line contains 2 * K names (names are made up of letters only). The first two identifiers correspond to the first column of banks, the next two identifiers to the middle column, and the last two to the last column of banks.

After the lines with placement in the benches are finished, a line with the upset identifier appears. Below this line are two students (their names) who are upset with each other. Below is the "listen:" identifier, followed by the number M, and then the children listened to are listed.

#### Example of input:
```
ionel alina teo eliza carmen monica
george diana bob liber nadia mihai
liber costin anda bogdan dora marin
luiza simona dana cristian tamara dragos
mihnea razvan radu patricia gigel elena
liber andrei oana victor liber dorel
viorel alex ela nicoleta maria gabi
suparati
george ionel
ela nicoleta
victor oana
teo eliza
teo luiza
elena dragos
alina dragos
ascultati:
4 
monica
maria
mesaj: ionel -> dragos
```
Here we can see the route of the ticket:
```
ionel > alina v diana v costin v simona v razvan v andrei >> oana ^ radu > patricia v victor v nicoleta >> maria > gabi ^ dorel ^ elena < gigel ^ tamara > dragos
```
#### Types of inputs tested:
- **input1.in** which has no solutions.
- **input2.in** where the initial state is equal to the final state.
- **input3.in** where no algorithm is blocked.
- **input4.in** which crashes for certain algorithms (exceeds the timeout we set).

## How to run this project

To run this project we need to get the arguments from the command line. Otherwise project will run with default arguments:
- Folder with input files: 'folder_input'
- Folder with output files: 'folder_output'
- Number of solutions searched: 1
- Timeout: 100s

The arguments we receive from the command line are:
- _**-i**_ or _**--input**_ for the path to the folder containing the input files.
- _**-o**_ or _**--output**_ for the path to the folder containing the output files.
- _**-n**_ or _**--nrsolutions**_ for the number of solutions to be generated.
- _**-t**_ or _**--timeout**_ for the time in seconds after which we want the program to stop
for a certain algorithm from generating solutions (for example, for timeout = 60s, if
the DFI algorithm exceeds this allotted time, it will stop and move to the next algorithm etc).

### Example of running this project
```
python main.py -i folder_input -o folder_output -n 5 -t 30
```
You can also use if you want `python3` instead of `python` :smile:
## Heuristics
#### Trivial heuristic
- Returns 1 if we are in the final state (if the ticket is on the position of the destination child), 0.
otherwise.
#### Admissible heuristic 1
- Returns the distance of Manhattan from the current position to the position of the destination child.
- **NOTE!** It is admissible because in order to reach the destination child you have to go through
at least the Manhattan distance (number of children; difference of children per line + difference of children
on the column), so he can only walk one step away from a child next to him.
#### Admissible heuristic 2
- Returns the Euclidean distance from the current position to the position of the target child (se
considers the distance 1 between 2 children in consecutive rows or (exclusively) columns).
- **NOTE!** It is admissible because it is always less than the distance from Manhattan.
Unfortunately, it is observed that on this particular issue, on some examples of input is
behaves slower and others faster.
#### Non-dmissible heuristic
- Returns 50 * the number of rows in the array * the number of columns in the array + the row
current + current column, to this value is added 2 ^ ((current line + column
current) ^ 10) only if the ticket stands still.
- **NOTE!** A move has a maximum cost of 2, and the ticket stays in place very rarely, so the factor is 50
to be multiplied by the number of children in the class makes this heuristic in some cases to be
inadmissible. I noticed that he tended to stay on the inadmissible heuristic
in the same place, so I added the term 2 ^ ((x + y) ^ 10), to force it to move
and not to give excessive timeout.

## Validations and optimizations
#### Validations
- Even number of students
```python
while "suparati" not in linie:
    linie = linie.split()
    if i == 0:
        assert len(linie) % 2 == 0, "nr impar de elevi pe prima linie"
    else:
        assert len(linie) == len(self.matrice[-1]), "nr diferit de elevi pe randul {}".format(i)
    self.matrice.append(linie)
```
- Different number of students per line
- Student with the same name
```python
for elev in self.matrice[-1]:
    if elev != "liber":
        assert elev not in self.pozitie_elev, "elev cu acelasi nume {}".format(elev)
        self.suparati[elev] = []
        self.pozitie_elev[elev] = (i, j)
    j += 1
linie = fin.readline()
i += 1
```
- Student who is called upset
- Otherwise, exceptions to the transformations are thrown (example, the listening time is missing on
student)
- If we don't have any students listened to
- If a non-existent student is listened to or the ticket moves from / to a non-existent student
there is throw exception with that name
- The number of solutions and the timeout received on the command line must be greater> 0
```python
assert nSol > 0, "nSol must be > 0"
assert timeout > 0, "timeout must be > 0"
```

#### Optimizations
- Finding a way to represent the state as efficiently as possible.
  * Current state = node
- Verifying that the scope node is different from the original node through the function
test_initial_scope.
```python
def testeaza_scop_initial(self, start: NodParcurgere, nrSolutiiCautate):
    if self.testeaza_scop(start):
        end_time = time.time()
        fout.write("Solution:\n")
        start.afisDrum(True, True)
        fout.write("Time: " + str(round(end_time - self.start_time, 4)) + "s\n")
        fout.write("\n----------------\n")
        nrSolutiiCautate -= 1
        if nrSolutiiCautate == 0:
            fout.write("The total number of nodes calculated: " + str(self.total) + "\n")
            fout.write("The maximum number of nodes in the memory: " + str(self.maxim) + "\n")
    return nrSolutiiCautate
```
- The most efficient implementation of the algorithms with which the program runs, possibly using
modules that provide high-performance data structures.
  * The Breadth First algorithm is implemented with Queue.
  * The A * algorithm is implemented with PriorityQueue.
  * The A * Optimized algorithm is implemented with Heapq, we also defined the operators `__lt__` and
`__eq__` to be able to compare nodes more efficiently. The order is ascending
after f, and if the fs are equal, descending after g.

## Algorithm Comparison

We will search for a solution for each algorithm. :smile:

#### for input3:
|Type of algorithm used  |Type of heuristic used  |Solution length|Solution cost | Total number of nodes generated|Maximum number of nodes in memory |The time to find the solution|
|--|--|--|--|--|--|--|
|BFS|-|18|12|1458232|872483|19.3483s| 
|DFS|-|20|6|565|19|0.004s| 
|DFI|-|18|12|2453996|17|19.2805s|  
|A*|Trivial|18|4|1553|875|0.018s| 
|A*|Admissible 1|18|4|131|83|0.002s| 
|A*|Admissible 2|18|4|135|85|0.001s| 
|A*|Non-admissible|18|8|1658762|799972|31.2023s| 
|A* OPT|Trivial|18|4|255|50|0.0039894s| 
|A* OPT|Admissible 1|18|4|70|27|0.0009987s| 
|A* OPT|Admissible 2|18|4|41|41|0.0009973s| 
|A* OPT|Non-admissible|18|14|111|22|0.005984s| 
|IDA*|Trivial|18|4|4912|20|0.0369s| 
|IDA*|Admissible 1|18|4|69|17|0.001s| 
|IDA*|Admissible 2|18|4|133|17|0.002s| 
|IDA*|Non-admissible|18|8|808687|28|8.243s|

#### for input4 (with timeout set default 100s):
|Type of algorithm used  |Type of heuristic used  |Solution length|Solution cost | Total number of nodes generated|Maximum number of nodes in memory |The time to find the solution|
|--|--|--|--|--|--|--|
|BFS|-|21|17|1795887|1096865|20.96s| 
|DFS|-|39|35|101|38|0.001s| 
|DFI|-|21|17|2979438|20|26.2698s|  
|A*|Trivial|-|-|5785486|3364216|TLE| 
|A*|Admissible 1|23|17|80622|40524|1.3643s| 
|A*|Admissible 2|25|17|102865|56417|1.7952s| 
|A*|Non-admissible|-|-|4263386|1434622|TLE| 
|A* OPT|Trivial|23|17|773|196|0.015826s| 
|A* OPT|Admissible 1|23|17|63|57|0.000997s| 
|A* OPT|Admissible 2|21|17|215|119|0.003989s| 
|A* OPT|Non-admissible|23|17|452|247|0.015957s| 
|IDA*|Trivial|-|-|9617440|32|TLE| 
|IDA*|Admissible 1|21|17|116657|24|1.0712s| 
|IDA*|Admissible 2|21|17|1518234|25|14.6498s| 
|IDA*|Non-admissible|-|-|7844680|35|TLE|

## Conclusion
_**A * optimized**_ is the best choice. It is also observed that it behaves best with
Manhattan or Euclidean heuristics. This is because our problem requires us to
we only go to neighboring students at every step. :smile: :heart:

We notice on our problem that **_IDA *_** runs the hardest (repeats calculations), but has the most
few nodes held in memory (maximum number is 35). So it takes up little memory, but the finding time of
of a solution is great. :thinking:
