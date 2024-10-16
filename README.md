# ExpNo 5: Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game

### Name: Pradeep kumar R 
### Register Number/Staff Id: 212223220077

### Aim:
Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game.

### Theory and Procedure:
To begin, let's start by defining what it means to play a perfect game of tic tac toe:

If I play perfectly, every time I play I will either win the game, or I will draw the game. Furthermore, if I play against another perfect player, I will always draw the game.

How might we describe these situations quantitatively? Let's assign a score to the "end game conditions:"

- I win, hurray! I get 10 points!
- I lose, that's unfortunate. I lose 10 points (because the other player gets 10 points).
- I draw, it's neutral. I get zero points, nobody gets any points.

So now we have a situation where we can determine a possible score for any game end state.

#### Example
To apply this, let's take an example from near the end of a game, where it is my turn. I am X. My goal here is to maximize my end game score.

![Tic-Tac-Toe Example](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/498656fc-79ce-4234-a623-06568bad8dda)

If the top of this image represents the state of the game I see when it is my turn, then I have some choices to make. There are three places I can play, one of which clearly results in me winning and earning the 10 points. If I don't make that move, O could very easily win, and I don't want O to win, so my goal here should be to pick the maximum scoring move.

#### What About O?
O, being the opponent, will try to minimize my score. From O's perspective, starting with the two other game states from above in which I don't immediately win:

![Tic-Tac-Toe Opponent Example](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/029b1a70-e92e-46c0-9a32-d6aea98ecd9d)

The choice is clear. O would pick any of the moves that result in a score of -10 for me.

#### Describing Minimax
The key to the Minimax algorithm is the back and forth between the two players, where each player desires to either maximize (for X) or minimize (for O) the score.

##### Minimax Algorithm:
- If the game is over, return the score from X's perspective.
- Otherwise, get a list of new game states for every possible move.
- Create a scores list.
- For each of these states, add the minimax result of that state to the scores list.
- If it's X's turn, return the maximum score from the scores list.
- If it's O's turn, return the minimum score from the scores list.

This algorithm is recursive, switching between the players until a final score is found.

Let's walk through the algorithm's execution with the full move tree, and show why, algorithmically, the instant winning move will be picked:

![Minimax Example](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/12b82542-54fb-47e7-8f76-b75fddc40f92)

1. It's X's turn in state 1. X generates the states 2, 3, and 4 and calls minimax on those states.
2. State 2 pushes the score of +10 to state 1's score list because the game is in an end state.
3. State 3 and 4 are not in end states, so state 3 generates states 5 and 6 and calls minimax on them, while state 4 generates states 7 and 8 and calls minimax on them.
4. State 5 pushes a score of -10 onto state 3's score list, and similarly, state 7 pushes a score of -10 onto state 4's score list.
5. State 6 and 8 generate the only available moves, which are end states, and so both of them add the score of +10 to the move lists of states 3 and 4.
6. Since it is O's turn in both state 3 and 4, O will seek to minimize the score, and both states 3 and 4 will yield -10.
7. Finally, the score list for states 2, 3, and 4 is populated with +10, -10, and -10 respectively, and state 1, seeking to maximize the score, will choose the winning move with score +10, which is state 2.

#### Code Version of Minimax
Here's the function for scoring the game:

```python
import time

class Game:
    def __init__(self):
        self.initialize_game()

    def initialize_game(self):
        self.current_state = [['.','.','.'],
                              ['.','.','.'],
                              ['.','.','.']]

        # Player X always plays first
        self.player_turn = 'X'

    def draw_board(self):
        for i in range(0, 3):
            for j in range(0, 3):
                print('{}|'.format(self.current_state[i][j]), end=" ")
            print()
        print()
    def is_valid(self, px, py):
        if px < 0 or px > 2 or py < 0 or py > 2:
            return False
        elif self.current_state[px][py] != '.':
            return False
        else:
            return True
    def is_end(self):
    # Vertical win
        for i in range(0, 3):
            if (self.current_state[0][i] != '.' and
                self.current_state[0][i] == self.current_state[1][i] and
                self.current_state[1][i] == self.current_state[2][i]):
                return self.current_state[0][i]

        # Horizontal win
        for i in range(0, 3):
            if (self.current_state[i] == ['X', 'X', 'X']):
                return 'X'
            elif (self.current_state[i] == ['O', 'O', 'O']):
                return 'O'

    # Main diagonal win
        if (self.current_state[0][0] != '.' and
            self.current_state[0][0] == self.current_state[1][1] and
            self.current_state[0][0] == self.current_state[2][2]):
            return self.current_state[0][0]

    # Second diagonal win
        if (self.current_state[0][2] != '.' and
            self.current_state[0][2] == self.current_state[1][1] and
            self.current_state[0][2] == self.current_state[2][0]):
            return self.current_state[0][2]

    # Is the whole board full?
        for i in range(0, 3):
            for j in range(0, 3):
            # There's an empty field, we continue the game
                if (self.current_state[i][j] == '.'):
                    return None

    # It's a tie!
        return '.'
    def max(self):

        # Possible values for maxv are:
        # -1 - loss
        # 0  - a tie
        # 1  - win

        # We're initially setting it to -2 as worse than the worst case:
        maxv = -2

        px = None
        py = None

        result = self.is_end()

        # If the game came to an end, the function needs to return
        # the evaluation function of the end. That can be:
        # -1 - loss
        # 0  - a tie
        # 1  - win
        if result == 'X':
            return (-1, 0, 0)
        elif result == 'O':
            return (1, 0, 0)
        elif result == '.':
            return (0, 0, 0)

        for i in range(0, 3):
            for j in range(0, 3):
                if self.current_state[i][j] == '.':
                    # On the empty field player 'O' makes a move and calls Min
                    # That's one branch of the game tree.
                    self.current_state[i][j] = 'O'
                    (m, min_i, min_j) = self.min()
                    # Fixing the maxv value if needed
                    if m > maxv:
                        maxv = m
                        px = i
                        py = j
                    # Setting back the field to empty
                    self.current_state[i][j] = '.'
        return (maxv, px, py)

    def min(self):

        # Possible values for minv are:
        # -1 - win
        # 0  - a tie
        # 1  - loss

        # We're initially setting it to 2 as worse than the worst case:
        minv = 2

        qx = None
        qy = None

        result = self.is_end()

        if result == 'X':
            return (-1, 0, 0)
        elif result == 'O':
            return (1, 0, 0)
        elif result == '.':
            return (0, 0, 0)

        '''
        TYPE THE CODE HERE BY REFERRING TO THE BELOW EXPLANATION.
        Nested Loops (for i in range(0, 3), for j in range(0, 3)):

These loops iterate over all the cells of the 3x3 Tic-Tac-Toe board (current_state), checking each cell's position (i, j).
Checking for an Empty Cell (if self.current_state[i][j] == '.'):

The condition self.current_state[i][j] == '.' ensures that the AI only considers moves in cells that are currently empty. Empty cells are represented by ..
Simulating a Move (self.current_state[i][j] = 'O'):

If the cell is empty, the AI temporarily places its marker 'O' in that position, simulating a potential move.
Recursive Minimax Call ((m, min_i, min_j) = self.min()):

After making the move, the AI calls the min() function. This function simulates the opponent’s (player 'X') move, assuming the opponent will play optimally.
The function min() will return a value m which represents the best possible outcome for the opponent if the AI makes this move.
Evaluating the Move (if m > maxv:):

The AI wants to maximize its chances of winning, so it compares the result m of the simulated move to the current best score (maxv).
If the simulated move is better than any previously considered moves, it updates maxv (the best value for player 'O'), and stores the coordinates (px, py) of the current best move.
Undoing the Simulated Move (self.current_state[i][j] = '.'):

After evaluating the move, the AI resets the cell back to empty (.), undoing the simulated move. This is necessary to test other possible moves in other cells.
Returning the Best Move (return (maxv, px, py)):

After evaluating all possible moves, the function returns the best possible outcome (maxv) and the coordinates (px, py) of the best move for player 'O'.
        '''
    def play(self):
        while True:
            self.draw_board()
            self.result = self.is_end()

            # Printing the appropriate message if the game has ended
            if self.result != None:
                if self.result == 'X':
                    print('The winner is X!')
                elif self.result == 'O':
                    print('The winner is O!')
                elif self.result == '.':
                    print("It's a tie!")

                self.initialize_game()
                return

            # If it's player's turn
            if self.player_turn == 'X':

                while True:

                    start = time.time()
                    (m, qx, qy) = self.min()
                    end = time.time()
                    print('Evaluation time: {}s'.format(round(end - start, 7)))
                    print('Recommended move: X = {}, Y = {}'.format(qx, qy))

                    px = int(input('Insert the X coordinate: '))
                    py = int(input('Insert the Y coordinate: '))

                    (qx, qy) = (px, py)

                    if self.is_valid(px, py):
                        self.current_state[px][py] = 'X'
                        self.player_turn = 'O'
                        break
                    else:
                        print('The move is not valid! Try again.')

            # If it's AI's turn
            else:
                (m, px, py) = self.max()
                self.current_state[px][py] = 'O'
                self.player_turn = 'X'
def main():
    g = Game()
    g.play()

if __name__ == "__main__":
    main()

```

## Sample Input and Output

![Sample 1](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/6b668685-8bcc-43c5-b5c2-ddd43f3da84a)
![Sample 2](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/8ca1b08a-8312-4ef5-89df-e69b7b2c3fa2)
![Sample 3](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/dc06427a-d4ce-43a1-95bd-9acfaefac323)
![Sample 4](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/a8a27e2a-6fd4-46a2-afb5-6d27b8556702)
![Sample 5](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/a2acb6a1-ed8e-42e5-8968-fe805e4b0255)

---

## Result:

Thus, the implementation of the Minimax Search Algorithm for a Simple TIC-TAC-TOE game was done successfully.

