package flashcards;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileWriter;
import java.util.*;

public class Main {

    private static String fileToExport;
    private static boolean isExport = false;
    private static boolean isFinish = false;
    private static final Map<String, String> map = new HashMap<>();
    private static final Map<String, Integer> errors = new HashMap<>();
    private static final List<String> list = new ArrayList<>();
    private static final Scanner scanner = new Scanner(System.in);

    static void menu() {
        
        System.out.println("Input the action (add, remove, import, export, ask, exit, log, hardest card, reset stats):");
        String choice = scanner.nextLine().trim();

        switch (choice) {
            case "add":
                printMsg("The card:");
                String card = scanner.nextLine().trim();

                if (map.containsKey(card)) {
                    printMsg("The card \"" + card + "\" already exists.");
                    menu();
                    break;
                }

                printMsg("The definition of the card:");
                String definition = scanner.nextLine().trim();

                if (map.containsValue(definition)) {
                    printMsg("The definition \"" + definition + "\" already exists.");
                    menu();
                    break;
                }

                map.put(card, definition);
                errors.put(card, 0);
                printMsg("The pair (\"" + card + "\":\"" + definition +
                        "\") has been added.");
                menu();
                break;

            case "remove":
                System.out.println("The card:");
                String cardToRemove = scanner.nextLine().trim();
                if (!map.containsKey(cardToRemove)) {
                    printMsg("Can't remove \"" +
                            cardToRemove + "\": there is no such card.");
                    menu();
                    break;
                } else {
                    map.remove(cardToRemove);
                    errors.remove(cardToRemove);

                    printMsg("The card has been removed.");
                }
                menu();
                break;

            case "import":
                printMsg("File name:");
                String fileNameToImport = scanner.nextLine();
                importToFile(fileNameToImport, false);
                break;

            case "export":
                printMsg("File name:");
                String fileNameToExport = scanner.nextLine();
                exportToFile(fileNameToExport);
                break;

            case "ask":
                ask();
                break;

            case "exit":
                printMsg("Bye bye!");
                if (isExport) {
                    isFinish = true;
                    exportToFile(fileToExport);
                }
                break;

            case "log":
                log();
                break;

            case "hardest card":
                findHardest();
                break;

            case "reset stats":
                for (var entry : errors.entrySet()) {
                    entry.setValue(0);
                }
                printMsg("Card statistics have been reset.");
                menu();
                break;

            default:
                break;
        }
    }

    public static void main(String[] args) {

        for (int i = 0; i < args.length; i++) {
            switch(args[i]) {
                case "-import":
                    importToFile(args[i + 1], true);
                    break;

                case "-export":
                    isExport = true;
                    fileToExport = args[i + 1];
                    break;

                default:
                    break;
            }
        }
        menu();
    }

    public static void ask() {
        Random random = new Random();

        printMsg("How many times to ask?");
        int nQuestions = Integer.parseInt(scanner.nextLine());

        for (int i = 0; i < nQuestions; i++) {
            int numberCard = random.nextInt(map.size());
            Object question = map.keySet().toArray()[numberCard];
            Object answer = map.get(question);
            printMsg("Print the definition of \"" + question + "\":");

            String userAnswer = scanner.nextLine();

            if (userAnswer.equals(answer)) {
                printMsg("Correct!");
            } else {
                errors.put((String) question, errors.getOrDefault(question, 0) + 1);

                if (map.containsValue(userAnswer)) {
                    printMsg("Wrong. The right answer is \"" + answer +
                            "\", but your definition is correct for \"" +
                            findKeyFromValue(userAnswer) + "\"");
                } else {
                    printMsg("Wrong. The right answer is \"" + answer + "\".");
                }
            }
        }
        menu();
    }

    static String findKeyFromValue (String value) {
        String s = "";
        for (var entry : map.entrySet()) {
            if (value.equals(entry.getValue())) {
                s = entry.getKey();
                break;
            }
        }
        return s;
    }

    static void findHardest() {
        boolean isMultipleHardest = false;
        int max = 0;
        List<String> hardestCard = new ArrayList<>();

        for (var entry : errors.entrySet()) {
            if (entry.getValue() > max) {
                max = entry.getValue();
                hardestCard.clear();                    // clear if max was found
                hardestCard.add(entry.getKey());
                isMultipleHardest = false;
            } else if (entry.getValue() == max && max > 0) {
                isMultipleHardest = true;
                hardestCard.add(entry.getKey());
            }
        }
        if (isMultipleHardest) {
            System.out.print("The hardest cards are ");
            for (int i = 0; i < hardestCard.size(); i++) {
                System.out.print("\"" + hardestCard.get(i) + "\"");
                System.out.print(i < hardestCard.size() - 1 ? ", " : ". ");
            }
            printMsg("You have " + max + " errors answering them.");
        } else {
            if (max == 0) {
                printMsg("There are no cards with errors.");
            } else {
                printMsg("The hardest card is \"" + hardestCard.get(0) +
                        "\". You have " + max + " errors answering it.");
            }
        }
        menu();
    }

    static void printMsg(String s) {

        System.out.println(s);
        Main.list.add(s);
    }

    static void log() {

        System.out.println("\nFile name:");
        String path = scanner.nextLine();

        try {
            File file = new File(path);
            FileWriter writer = new FileWriter(file);

            for (String s : Main.list) {
                writer.write(s);
            }

            writer.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("\nThe log has been saved.");
        menu();
    }

    static void importToFile(String pathToFile, boolean isCommandLine) {

        int count = 0;

        try {
            File file = new File(pathToFile);
            Scanner fileScanner = new Scanner(file);

            while (fileScanner.hasNextLine()) {

                String[] str = fileScanner.nextLine().split(":");

                map.put(str[0], str[1]);

                int errorsFromFile = str[2].equals("null") ? 0 : Integer.parseInt(str[2]);

                errors.put(str[0], errorsFromFile);
                count++;
            }
        if (count > 0 || !isCommandLine) {
            printMsg(count + " cards have been loaded.");
        }
        } catch (FileNotFoundException e) {
            printMsg("File not found.");
            e.printStackTrace();
        }
        if (!isCommandLine) {
            menu();
        }
    }

    static void exportToFile(String fileNameToExport) {

        int count1 = 0;

        try {
            File file2 = new File(fileNameToExport);
            FileWriter writer = new FileWriter(file2);

            for (var entry : map.entrySet()) {
                writer.write(entry.getKey() + ":" + entry.getValue() +
                        ":" + errors.get(entry.getKey()) + "\n");
                count1++;
            }
            writer.close();
                printMsg(count1 + " cards have been saved.");

        } catch (Exception e) {
            e.printStackTrace();
        }
        if (!isFinish) {
            menu();
        }
    }
}
