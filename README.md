import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class SecretSharing {

    // Method to convert a number from a given base to decimal
    public static long baseToDecimal(int base, String value) {
        return Long.parseLong(value, base);
    }

    // Method to perform Lagrange interpolation and find the constant term c
    public static long findConstantTerm(List<Long> xValues, List<Long> yValues) {
        long c = 0; // This will store the constant term

        // Calculate the constant term using Lagrange interpolation
        for (int i = 0; i < xValues.size(); i++) {
            long xi = xValues.get(i);
            long yi = yValues.get(i);
            long term = yi;

            for (int j = 0; j < xValues.size(); j++) {
                if (i != j) {
                    term *= (0 - xValues.get(j));
                    term /= (xi - xValues.get(j));
                }
            }
            c += term;
        }
        return c;
    }

    public static void main(String[] args) {
        // Read JSON input from a file
        String jsonFilePath = "input.json"; // Replace with your input file path
        StringBuilder jsonInput = new StringBuilder();

        try (BufferedReader br = new BufferedReader(new FileReader(jsonFilePath))) {
            String line;
            while ((line = br.readLine()) != null) {
                jsonInput.append(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

        // Parse the JSON input
        JSONObject data = new JSONObject(jsonInput.toString());
        List<Long> xValues = new ArrayList<>();
        List<Long> yValues = new ArrayList<>();

        // Extract x and y values from the JSON input
        for (String key : data.keySet()) {
            if (key.matches("\\d+")) { // Check if the key is numeric
                JSONObject rootData = data.getJSONObject(key);
                int base = rootData.getInt("base");
                String value = rootData.getString("value");
                long yValue = baseToDecimal(base, value);
                long xValue = Long.parseLong(key); // x is the key itself
                xValues.add(xValue);
                yValues.add(yValue);
            }
        }

        // Find the constant term c
        long c = findConstantTerm(xValues, yValues);
        System.out.println("The constant term c of the polynomial is: " + c);
    }
}
