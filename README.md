# mayin_tarlasi

import java.util.Random;

import java.util.Scanner;

class MineSweeper {
    private String[][] mineMap;      // Mayınların yerleştirildiği harita
    private String[][] gameMap;      // Oyuncunun göreceği harita
    private int rows;                // Satır sayısı
    private int cols;                // Sütun sayısı
    private int totalMines;          // Toplam mayın sayısı
    private boolean gameRunning;     // Oyun durumu

    public MineSweeper(int rows, int cols) {
        if (rows < 2 || cols < 2) {
            throw new IllegalArgumentException("Matris boyutları en az 2x2 olmalıdır.");
        }
        this.rows = rows;
        this.cols = cols;
        this.totalMines = (rows * cols) / 4;
        this.mineMap = new String[rows][cols];
        this.gameMap = new String[rows][cols];
        this.gameRunning = true;

        initializeMaps();
    }

    // Haritaları başlatır
    private void initializeMaps() {
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                mineMap[i][j] = "-";
                gameMap[i][j] = "-";
            }
        }
        placeMines();
    }

    // Mayınları rastgele yerleştirir
    private void placeMines() {
        Random random = new Random();
        int placedMines = 0;

        while (placedMines < totalMines) {
            int r = random.nextInt(rows);
            int c = random.nextInt(cols);

            if (!mineMap[r][c].equals("*")) {
                mineMap[r][c] = "*";
                placedMines++;
            }
        }
    }

    // Haritayı yazdırır
    private void printMap(String[][] map) {
        for (String[] row : map) {
            for (String cell : row) {
                System.out.print(cell + " ");
            }
            System.out.println();
        }
    }

    // Komşu mayın sayısını hesaplar
    private int countAdjacentMines(int r, int c) {
        int count = 0;
        int[][] directions = {
            {-1, -1}, {-1, 0}, {-1, 1},
            {0, -1},           {0, 1},
            {1, -1}, {1, 0}, {1, 1}
        };

        for (int[] dir : directions) {
            int newRow = r + dir[0];
            int newCol = c + dir[1];

            if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols && mineMap[newRow][newCol].equals("*")) {
                count++;
            }
        }
        return count;
    }

    // Kullanıcıdan koordinat alır
    private int[] getUserInput(Scanner scanner) {
        int[] input = new int[2];

        while (true) {
            System.out.print("Satır girin: ");
            input[0] = scanner.nextInt();
            System.out.print("Sütun girin: ");
            input[1] = scanner.nextInt();

            if (input[0] >= 0 && input[0] < rows && input[1] >= 0 && input[1] < cols) {
                if (!gameMap[input[0]][input[1]].equals("-")) {
                    System.out.println("Bu koordinat daha önce seçildi, başka bir koordinat girin.");
                } else {
                    break;
                }
            } else {
                System.out.println("Geçersiz koordinat, lütfen tekrar deneyin.");
            }
        }
        return input;
    }

    // Oyunu başlatır
    public void play() {
        Scanner scanner = new Scanner(System.in);
        int openedCells = 0;
        int safeCells = (rows * cols) - totalMines;

        System.out.println("Mayın Tarlası Oyunu Başladı!");
        while (gameRunning) {
            printMap(gameMap);
            int[] userInput = getUserInput(scanner);
            int row = userInput[0];
            int col = userInput[1];

            if (mineMap[row][col].equals("*")) {
                System.out.println("Mayına bastınız! Oyunu kaybettiniz.");
                gameRunning = false;
                printMap(mineMap); // Mayın haritasını göster
                break;
            } else {
                int adjacentMines = countAdjacentMines(row, col);
                gameMap[row][col] = String.valueOf(adjacentMines);
                openedCells++;

                if (openedCells == safeCells) {
                    System.out.println("Tebrikler! Tüm güvenli hücreleri açtınız. Oyunu kazandınız!");
                    gameRunning = false;
                    break;
                }
            }
        }
        scanner.close();
    }
}

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.println("Mayın Tarlası Oyununa Hoş Geldiniz!");
        int rows, cols;

        while (true) {
            System.out.print("Satır sayısını girin: ");
            rows = scanner.nextInt();
            System.out.print("Sütun sayısını girin: ");
            cols = scanner.nextInt();

            if (rows >= 2 && cols >= 2) {
                break;
            } else {
                System.out.println("Lütfen en az 2x2 boyutunda bir matris girin.");
            }
        }

        MineSweeper game = new MineSweeper(rows, cols);
        game.play();
    }
}
