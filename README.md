import random
from queue import Queue

def generate_maze(rows, cols, wall_percentage):
    maze = [['◌' for _ in range(cols)] for _ in range(rows)]

    # Set start and end points
    start_point = (0, 0)
    end_point = (rows - 1, cols - 1)
    maze[start_point[0]][start_point[1]] = 'S'
    maze[end_point[0]][end_point[1]] = 'E'

    # Calculate the maximum number of walls based on the specified percentage
    max_walls = int(rows * cols * (wall_percentage / 100))

    # Add random walls to the maze
    for _ in range(max_walls):
        row, col = random.randint(0, rows - 1), random.randint(0, cols - 1)
        while (row, col) == start_point or (row, col) == end_point or maze[row][col] == '▓':
            row, col = random.randint(0, rows - 1), random.randint(0, cols - 1)
        maze[row][col] = '▓'  # '▓' represents a wall

    return maze, start_point, end_point

def print_maze(maze):
    for row in maze:
        for cell in row:
            if cell == 'S':
                print('\033[95m' + cell + '\033[0m', end=' ')  # Magenta for start
            elif cell == 'E':
                print('\033[95m' + cell + '\033[0m', end=' ')  # Magenta for end
            elif cell == '▓':
                print('\033[91m' + cell + '\033[0m', end=' ')  # Red for walls
            elif cell == '◌':
                print('\033[94m' + cell + '\033[0m', end=' ')  # Blue for open space
            else:
                print('\033[92m' + cell + '\033[0m', end=' ')  # Green for path
        print()

def find_path_bfs(maze, start, end):
    queue = Queue()
    visited = set()
    queue.put((start, []))

    while not queue.empty():
        current, path = queue.get()
        x, y = current

        if current == end:
            return path + [(x, y)]

        if current in visited:
            continue

        visited.add(current)

        moves = [(x+1, y), (x-1, y), (x, y+1), (x, y-1)]

        for move in moves:
            new_x, new_y = move
            if 0 <= new_x < len(maze) and 0 <= new_y < len(maze[0]) and maze[new_x][new_y] != '▓' and (new_x, new_y) not in visited:
                queue.put(((new_x, new_y), path + [(x, y)]))

    return None

def print_path(path):
    print("\nPath:")
    for x, y in path:
        print(f"({x}, {y}) -> ", end="")
    print("End")

def mark_path_on_maze(maze, path):
    for x, y in path:
        if maze[x][y] == '◌':
            maze[x][y] = '◍'

def main():
    rows = 5
    cols = 5
    wall_percentage = 20

    while True:
        maze, start_point, end_point = generate_maze(rows, cols, wall_percentage)

        print("\nCurrent Maze:")
        print_maze(maze)

        path = find_path_bfs(maze, start_point, end_point)

        if path:
            mark_path_on_maze(maze, path)
            print_path(path)
            print("\nMaze with Path:")
            print_maze(maze)
        else:
            print("\nNo path found.")

        print("\nOptions:")
        print("1. Generate Another Puzzle")
        print("2. Exit")

        choice = input("Enter your choice (1/2): ")

        if choice == '2':
            print("Exiting the game. Goodbye!")
            break

if __name__ == "__main__":
    main()
