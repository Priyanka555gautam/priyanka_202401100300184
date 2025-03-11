# priyanka_202401100300184
import math

# Constants
PLAYER_X = "X"  # AI
PLAYER_O = "O"  # Human
EMPTY = " "

# Initialize Board
def create_board():
    return [[EMPTY] * 3 for _ in range(3)]

# Check for winner
def check_winner(board):
    for row in board:
        if row[0] == row[1] == row[2] and row[0] != EMPTY:
            return row[0]

    for col in range(3):
        if board[0][col] == board[1][col] == board[2][col] and board[0][col] != EMPTY:
            return board[0][col]

    if board[0][0] == board[1][1] == board[2][2] and board[0][0] != EMPTY:
        return board[0][0]

    if board[0][2] == board[1][1] == board[2][0] and board[0][2] != EMPTY:
        return board[0][2]

    return None

# Check if board is full
def is_draw(board):
    return all(cell != EMPTY for row in board for cell in row)

# Get available moves
def get_available_moves(board):
    return [(r, c) for r in range(3) for c in range(3) if board[r][c] == EMPTY]

# Minimax with Alpha-Beta Pruning
def minimax(board, depth, alpha, beta, is_maximizing):
    winner = check_winner(board)
    if winner == PLAYER_X:
        return 10 - depth
    if winner == PLAYER_O:
        return depth - 10
    if is_draw(board):
        return 0

    if is_maximizing:
        max_eval = -math.inf
        for (r, c) in get_available_moves(board):
            board[r][c] = PLAYER_X
            eval = minimax(board, depth + 1, alpha, beta, False)
            board[r][c] = EMPTY
            max_eval = max(max_eval, eval)
            alpha = max(alpha, eval)
            if beta <= alpha:
                break  # Alpha-Beta Pruning
        return max_eval
    else:
        min_eval = math.inf
        for (r, c) in get_available_moves(board):
            board[r][c] = PLAYER_O
            eval = minimax(board, depth + 1, alpha, beta, True)
            board[r][c] = EMPTY
            min_eval = min(min_eval, eval)
            beta = min(beta, eval)
            if beta <= alpha:
                break  # Alpha-Beta Pruning
        return min_eval

# Best AI Move
def best_move(board):
    best_score = -math.inf
    move = None
    for (r, c) in get_available_moves(board):
        board[r][c] = PLAYER_X
        score = minimax(board, 0, -math.inf, math.inf, False)
        board[r][c] = EMPTY
        if score > best_score:
            best_score = score
            move = (r, c)
    return move

# Print Board
def print_board(board):
    for row in board:
        print(" | ".join(row))
        print("-" * 9)

# Main Game Loop
def play_game():
    board = create_board()
    human_turn = True

    while True:
        print_board(board)
        winner = check_winner(board)
        if winner:
            print(f"Winner: {winner}!")
            break
        if is_draw(board):
            print("It's a draw!")
            break

        if human_turn:
            move = None
            while move not in get_available_moves(board):
                try:
                    row, col = map(int, input("Enter row and column (0-2) separated by space: ").split())
                    move = (row, col)
                except ValueError:
                    pass
            board[row][col] = PLAYER_O
        else:
            print("AI is thinking...")
            row, col = best_move(board)
            board[row][col] = PLAYER_X

        human_turn = not human_turn

# Run the game
if __name__ == "__main__":
    play_game()
