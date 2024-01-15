server code:

import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

public class QuizServer {
    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(12345); // Server listens on port 12345
            System.out.println("Quiz Server is running. Waiting for players...");

            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("Player connected: " + clientSocket.getInetAddress().getHostAddress());
                
                // Create a new thread to handle each player
                QuizGameHandler handler = new QuizGameHandler(clientSocket);
                new Thread(handler).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}



QuizGameHandler class that will handle communication with each connected player:


import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.util.Scanner;

public class QuizGameHandler implements Runnable {
    private Socket clientSocket;
    private PrintWriter out;
    private BufferedReader in;
    private Scanner scanner;

    public QuizGameHandler(Socket clientSocket) {
        this.clientSocket = clientSocket;
        try {
            out = new PrintWriter(clientSocket.getOutputStream(), true);
            in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
            scanner = new Scanner(System.in); // For server input
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void run() {
        try {
            out.println("Welcome to the Quiz Game!");
            out.println("Answer the following question:");

            // Replace with your quiz questions
            String question = "What is the capital of France?";
            String correctAnswer = "Paris";

            out.println(question);
            out.println("Options:");
            out.println("A. Berlin");
            out.println("B. London");
            out.println("C. Paris");
            out.println("D. Rome");

            String userAnswer = in.readLine();

            if (userAnswer.equalsIgnoreCase(correctAnswer)) {
                out.println("Correct! You scored a point.");
            } else {
                out.println("Incorrect. The correct answer is " + correctAnswer);
            }

            out.println("Thanks for playing!");

            // Close resources
            out.close();
            in.close();
            clientSocket.close();

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

