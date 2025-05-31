# SCT_SD_03
Sudoku game
import javax.swing.*;
import java.awt.*;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.Random;

public class SudokuSolverGUI extends JFrame {
    private static final int SIZE = 9;
    private final JTextField[][] cells = new JTextField[SIZE][SIZE];
    private final Random random = new Random();

    public SudokuSolverGUI() {
        super("Sudoku Solver & Generator");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(500, 550);
        setLayout(new BorderLayout());

        JPanel grid = new JPanel(new GridLayout(SIZE, SIZE));
        Font font = new Font("Monospaced", Font.BOLD, 20);

        for (int row = 0; row < SIZE; row++) {
            for (int col = 0; col < SIZE; col++) {
                JTextField field = new JTextField();
                field.setHorizontalAlignment(JTextField.CENTER);
                field.setFont(font);
                cells[row][col] = field;
                grid.add(field);
            }
        }

        JButton solveBtn = new JButton("Solve");
        solveBtn.addActionListener(e -> {
            int[][] board = readGrid();
            if (solve(board)) {
                updateGrid(board);
            } else {
                JOptionPane.showMessageDialog(this, "This Sudoku can't be solved.");
            }
        });

        JButton generateBtn = new JButton("Generate");
        generateBtn.addActionListener(e -> {
            int[][] full = new int[SIZE][SIZE];
            solve(full);  // fill completely
            int[][] puzzle = removeCells(full, 40); // remove 40 for medium difficulty
            updateGrid(puzzle);
        });

        JPanel buttons = new JPanel();
        buttons.add(solveBtn);
        buttons.add(generateBtn);

        add(grid, BorderLayout.CENTER);
        add(buttons, BorderLayout.SOUTH);
        setVisible(true);
    }

    private int[][] readGrid() {
        int[][] board = new int[SIZE][SIZE];
        for (int r = 0; r < SIZE; r++) {
            for (int c = 0; c < SIZE; c++) {
                String text = cells[r][c].getText();
                board[r][c] = text.matches("\\d") ? Integer.parseInt(text) : 0;
            }
        }
        return board;
    }

    private void updateGrid(int[][] board) {
        for (int r = 0; r < SIZE; r++) {
            for (int c = 0; c < SIZE; c++) {
                cells[r][c].setText(board[r][c] == 0 ? "" : String.valueOf(board[r][c]));
            }
        }
    }

    private boolean solve(int[][] board) {
        for (int row = 0; row < SIZE; row++) {
            for (int col = 0; col < SIZE; col++) {
                if (board[row][col] == 0) {
                    List<Integer> nums = shuffledNumbers();
                    for (int num : nums) {
                        if (isValid(board, row, col, num)) {
                            board[row][col] = num;
                            if (solve(board)) return true;
                            board[row][col] = 0;
                        }
                    }
                    return false;
                }
            }
        }
        return true;
    }

    private boolean isValid(int[][] board, int row, int col, int num) {
        for (int i = 0; i < SIZE; i++) {
            if (board[row][i] == num || board[i][col] == num) return false;
        }
        int boxRow = row - row % 3, boxCol = col - col % 3;
        for (int r = boxRow; r < boxRow + 3; r++) {
            for (int c = boxCol; c < boxCol + 3; c++) {
                if (board[r][c] == num) return false;
            }
        }
        return true;
    }

    private List<Integer> shuffledNumbers() {
        List<Integer> list = new ArrayList<>();
        for (int i = 1; i <= SIZE; i++) list.add(i);
        Collections.shuffle(list);
        return list;
    }

    private int[][] removeCells(int[][] board, int count) {
        int[][] copy = new int[SIZE][SIZE];
        for (int r = 0; r < SIZE; r++) copy[r] = Arrays.copyOf(board[r], SIZE);

        while (count > 0) {
            int row = random.nextInt(SIZE);
            int col = random.nextInt(SIZE);
            if (copy[row][col] != 0) {
                copy[row][col] = 0;
                count--;
            }
        }
        return copy;
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(SudokuSolverGUI::new);
    }
}
