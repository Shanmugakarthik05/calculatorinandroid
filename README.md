# Ex.No:5 Develop a simple calculator using android studio.

## AIM:

To develop a program to develop a simple calculator in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as calculator and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout using UI components in activity_main.xml.

Step 6: Display the calculator operation in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the text “calculator operation”.
Developed by: SHANMUGAKARTHIK G
Registration Number : 212223220105
*/
```

#### MainActivity.java
```java
package com.example.sc;

import android.os.Bundle;
import android.view.KeyEvent;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import com.google.android.material.bottomsheet.BottomSheetDialog;
import com.google.android.material.button.MaterialButton;

import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.List;
import java.util.Locale;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView tvExpression, tvResult;
    private String currentExpression = "";
    private double memoryValue = 0;
    private boolean lastResultDisplayed = false;
    private boolean isScientificMode = false;
    private final List<String> history = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvExpression = findViewById(R.id.tvExpression);
        tvResult = findViewById(R.id.tvResult);

        setClickListeners();
    }

    private void setClickListeners() {
        int[] buttonIds = {
                R.id.btn0, R.id.btn1, R.id.btn2, R.id.btn3, R.id.btn4,
                R.id.btn5, R.id.btn6, R.id.btn7, R.id.btn8, R.id.btn9,
                R.id.btnDot, R.id.btnAdd, R.id.btnMinus, R.id.btnMultiply,
                R.id.btnDivide, R.id.btnMod, R.id.btnAC, R.id.btnC,
                R.id.btnEqual, R.id.btnMC, R.id.btnMR, R.id.btnMPlus, R.id.btnMMinus,
                R.id.btnToggleMode, R.id.btnSin, R.id.btnCos, R.id.btnTan,
                R.id.btnLog, R.id.btnLn, R.id.btnPi, R.id.btnE, R.id.btnFact,
                R.id.btnHistory
        };

        for (int id : buttonIds) {
            findViewById(id).setOnClickListener(this);
        }
    }

    @Override
    public void onClick(View v) {
        int id = v.getId();
        
        if (id == R.id.btnToggleMode) {
            toggleScientificMode();
            return;
        }

        if (id == R.id.btnHistory) {
            showHistoryDialog();
            return;
        }

        MaterialButton button = (MaterialButton) v;
        String buttonText = button.getText().toString();

        if (id == R.id.btnAC) {
            currentExpression = "";
            tvExpression.setText("");
            tvResult.setText(R.string.zero);
            lastResultDisplayed = false;
        } else if (id == R.id.btnC) {
            if (!currentExpression.isEmpty()) {
                currentExpression = currentExpression.substring(0, currentExpression.length() - 1);
                tvExpression.setText(currentExpression);
            }
        } else if (id == R.id.btnEqual) {
            calculateResult();
        } else if (id == R.id.btnMC) {
            memoryValue = 0;
            Toast.makeText(this, "Memory Cleared", Toast.LENGTH_SHORT).show();
        } else if (id == R.id.btnMR) {
            appendToExpression(formatValue(memoryValue));
        } else if (id == R.id.btnMPlus) {
            updateMemory(true);
        } else if (id == R.id.btnMMinus) {
            updateMemory(false);
        } else {
            if (lastResultDisplayed && isNumeric(buttonText)) {
                currentExpression = "";
                lastResultDisplayed = false;
            } else {
                lastResultDisplayed = false;
            }
            
            if (isScientificFunction(buttonText)) {
                appendToExpression(buttonText + "(");
            } else {
                appendToExpression(buttonText);
            }
        }
    }

    private void showHistoryDialog() {
        if (history.isEmpty()) {
            Toast.makeText(this, "No history available", Toast.LENGTH_SHORT).show();
            return;
        }

        BottomSheetDialog dialog = new BottomSheetDialog(this);
        ListView listView = new ListView(this);
        ArrayAdapter<String> adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, history);
        listView.setAdapter(adapter);
        
        listView.setOnItemClickListener((parent, v, position, id) -> {
            String entry = history.get(position);
            currentExpression = entry.split(" = ")[0];
            tvExpression.setText(currentExpression);
            dialog.dismiss();
        });

        dialog.setContentView(listView);
        dialog.show();
    }

    private void toggleScientificMode() {
        isScientificMode = !isScientificMode;
        MaterialButton btnToggle = findViewById(R.id.btnToggleMode);
        btnToggle.setText(isScientificMode ? R.string.mode_std : R.string.mode_sci);

        int visibility = isScientificMode ? View.VISIBLE : View.GONE;
        int[] sciButtons = {
                R.id.btnSin, R.id.btnCos, R.id.btnTan, R.id.btnPi,
                R.id.btnLog, R.id.btnLn, R.id.btnE, R.id.btnFact
        };
        for (int id : sciButtons) {
            findViewById(id).setVisibility(visibility);
        }
    }

    private boolean isScientificFunction(String text) {
        return text.equals("sin") || text.equals("cos") || text.equals("tan") || 
               text.equals("log") || text.equals("ln");
    }

    private void appendToExpression(String text) {
        if (isOperator(text) && !currentExpression.isEmpty()) {
            char lastChar = currentExpression.charAt(currentExpression.length() - 1);
            if (isOperator(String.valueOf(lastChar))) {
                currentExpression = currentExpression.substring(0, currentExpression.length() - 1);
            }
        }
        currentExpression += text;
        tvExpression.setText(currentExpression);
    }

    private boolean isOperator(String s) {
        return s.equals("+") || s.equals("−") || s.equals("×") || s.equals("÷") || s.equals("%") || s.equals("^");
    }

    private boolean isNumeric(String s) {
        try {
            Double.parseDouble(s);
            return true;
        } catch (NumberFormatException e) {
            return false;
        }
    }

    private void updateMemory(boolean plus) {
        try {
            double value = Double.parseDouble(tvResult.getText().toString());
            if (plus) memoryValue += value;
            else memoryValue -= value;
            Toast.makeText(this, "Memory Updated", Toast.LENGTH_SHORT).show();
        } catch (Exception e) {
            Toast.makeText(this, "Invalid Result", Toast.LENGTH_SHORT).show();
        }
    }

    private void calculateResult() {
        if (currentExpression.isEmpty()) return;

        try {
            StringBuilder sb = new StringBuilder(currentExpression
                    .replace("×", "*")
                    .replace("÷", "/")
                    .replace("−", "-")
                    .replace("π", String.valueOf(Math.PI))
                    .replace("e", String.valueOf(Math.E)));
            
            int openCount = 0;
            int closeCount = 0;
            for(int i=0; i<sb.length(); i++) {
                if(sb.charAt(i) == '(') openCount++;
                if(sb.charAt(i) == ')') closeCount++;
            }
            while(openCount > closeCount) {
                sb.append(")");
                closeCount++;
            }

            double result = eval(sb.toString());
            String formattedResult = formatValue(result);
            tvResult.setText(formattedResult);
            
            history.add(0, currentExpression + " = " + formattedResult);
            if (history.size() > 20) history.remove(history.size() - 1);
            
            lastResultDisplayed = true;
        } catch (Exception e) {
            tvResult.setText(R.string.error);
            lastResultDisplayed = false;
        }
    }

    private String formatValue(double value) {
        if (Double.isNaN(value) || Double.isInfinite(value)) return "Error";
        if (value == (long) value)
            return String.format(Locale.getDefault(), "%d", (long) value);
        else
            return new DecimalFormat("#.##########").format(value);
    }

    @Override
    public boolean onKeyUp(int keyCode, KeyEvent event) {
        switch (keyCode) {
            case KeyEvent.KEYCODE_0: appendToExpression("0"); return true;
            case KeyEvent.KEYCODE_1: appendToExpression("1"); return true;
            case KeyEvent.KEYCODE_2: appendToExpression("2"); return true;
            case KeyEvent.KEYCODE_3: appendToExpression("3"); return true;
            case KeyEvent.KEYCODE_4: appendToExpression("4"); return true;
            case KeyEvent.KEYCODE_5: appendToExpression("5"); return true;
            case KeyEvent.KEYCODE_6: appendToExpression("6"); return true;
            case KeyEvent.KEYCODE_7: appendToExpression("7"); return true;
            case KeyEvent.KEYCODE_8: appendToExpression("8"); return true;
            case KeyEvent.KEYCODE_9: appendToExpression("9"); return true;
            case KeyEvent.KEYCODE_PLUS: appendToExpression("+"); return true;
            case KeyEvent.KEYCODE_MINUS: appendToExpression("−"); return true;
            case KeyEvent.KEYCODE_STAR: appendToExpression("×"); return true;
            case KeyEvent.KEYCODE_SLASH: appendToExpression("÷"); return true;
            case KeyEvent.KEYCODE_PERIOD: appendToExpression("."); return true;
            case KeyEvent.KEYCODE_ENTER:
            case KeyEvent.KEYCODE_NUMPAD_ENTER:
            case KeyEvent.KEYCODE_EQUALS:
                calculateResult();
                return true;
            case KeyEvent.KEYCODE_DEL:
                if (!currentExpression.isEmpty()) {
                    currentExpression = currentExpression.substring(0, currentExpression.length() - 1);
                    tvExpression.setText(currentExpression);
                }
                return true;
            case KeyEvent.KEYCODE_ESCAPE:
                currentExpression = "";
                tvExpression.setText("");
                tvResult.setText(R.string.zero);
                lastResultDisplayed = false;
                return true;
        }
        return super.onKeyUp(keyCode, event);
    }

    public static double eval(final String str) {
        return new Object() {
            int pos = -1, ch;

            void nextChar() {
                ch = (++pos < str.length()) ? str.charAt(pos) : -1;
            }

            boolean eat(int charToEat) {
                while (ch == ' ') nextChar();
                if (ch == charToEat) {
                    nextChar();
                    return true;
                }
                return false;
            }

            double parse() {
                nextChar();
                double x = parseExpression();
                if (pos < str.length()) throw new RuntimeException("Unexpected: " + (char) ch);
                return x;
            }

            double parseExpression() {
                double x = parseTerm();
                for (;;) {
                    if (eat('+')) x += parseTerm();
                    else if (eat('-')) x -= parseTerm();
                    else return x;
                }
            }

            double parseTerm() {
                double x = parseFactor();
                for (;;) {
                    if (eat('*')) x *= parseFactor();
                    else if (eat('/')) x /= parseFactor();
                    else if (eat('%')) x %= parseFactor();
                    else return x;
                }
            }

            double parseFactor() {
                if (eat('+')) return parseFactor();
                if (eat('-')) return -parseFactor();

                double x;
                int startPos = this.pos;
                if (eat('(')) {
                    x = parseExpression();
                    eat(')');
                } else if ((ch >= '0' && ch <= '9') || ch == '.') {
                    while ((ch >= '0' && ch <= '9') || ch == '.') nextChar();
                    x = Double.parseDouble(str.substring(startPos, this.pos));
                } else if (ch >= 'a' && ch <= 'z') {
                    while (ch >= 'a' && ch <= 'z') nextChar();
                    String func = str.substring(startPos, this.pos);
                    x = parseFactor();
                    switch (func) {
                        case "sqrt": x = Math.sqrt(x); break;
                        case "sin": x = Math.sin(Math.toRadians(x)); break;
                        case "cos": x = Math.cos(Math.toRadians(x)); break;
                        case "tan": x = Math.tan(Math.toRadians(x)); break;
                        case "log": x = Math.log10(x); break;
                        case "ln": x = Math.log(x); break;
                        default: throw new RuntimeException("Unknown function: " + func);
                    }
                } else {
                    throw new RuntimeException("Unexpected: " + (char) ch);
                }

                if (eat('^')) x = Math.pow(x, parseFactor());
                if (eat('!')) {
                    x = factorial((int) x);
                }

                return x;
            }

            double factorial(int n) {
                if (n < 0) return Double.NaN;
                double res = 1;
                for (int i = 2; i <= n; i++) res *= i;
                return res;
            }
        }.parse();
    }
}
```

#### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/colorBackground"
    tools:context=".MainActivity">

    <!-- Display Area -->
    <com.google.android.material.card.MaterialCardView
        android:id="@+id/displayCard"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_margin="20dp"
        app:cardBackgroundColor="@color/colorDisplayCard"
        app:cardCornerRadius="24dp"
        app:cardElevation="8dp"
        app:strokeWidth="0dp"
        app:layout_constraintBottom_toTopOf="@+id/guideline"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="bottom|end"
            android:orientation="vertical"
            android:padding="24dp">

            <TextView
                android:id="@+id/tvExpression"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textColor="@color/colorTextSecondary"
                android:textSize="22sp"
                android:letterSpacing="0.05"
                tools:text="1,234 × 5" />

            <TextView
                android:id="@+id/tvResult"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/zero"
                android:textColor="@color/colorTextDisplay"
                android:textSize="56sp"
                android:textStyle="bold"
                android:includeFontPadding="false" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guideline"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_percent="0.32" />

    <!-- Controls Row -->
    <LinearLayout
        android:id="@+id/controlsRow"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:gravity="center_vertical"
        android:orientation="horizontal"
        app:layout_constraintBottom_toTopOf="@+id/keypad"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/displayCard">

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnHistory"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/history"
            android:textColor="@color/colorFunctionText"
            app:icon="@android:drawable/ic_menu_recent_history"
            app:iconTint="@color/colorFunctionText" />

        <View
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_weight="1" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnToggleMode"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/mode_sci"
            android:textColor="@color/colorOperatorText" />
    </LinearLayout>

    <!-- Keypad Area -->
    <GridLayout
        android:id="@+id/keypad"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_margin="12dp"
        android:columnCount="4"
        android:rowCount="7"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/controlsRow">

        <!-- Scientific Row (Visible only in Sci Mode) -->
        <!-- All sci buttons use colorFunction style -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnSin"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/sin"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnCos"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/cos"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnTan"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/tan"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnPi"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/pi"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnLog"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/log"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnLn"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/ln"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnE"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/e"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnFact"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="4dp"
            android:text="@string/fact"
            android:textColor="@color/colorFunctionText"
            android:visibility="gone" />

        <!-- Row 0: Memory -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMC"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="2dp"
            android:text="@string/mc"
            android:textColor="@color/colorFunctionText"
            android:textSize="12sp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMR"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="2dp"
            android:text="@string/mr"
            android:textColor="@color/colorFunctionText"
            android:textSize="12sp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMPlus"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="2dp"
            android:text="@string/m_plus"
            android:textColor="@color/colorFunctionText"
            android:textSize="12sp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMMinus"
            style="@style/Widget.Material3.Button.TextButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="2dp"
            android:text="@string/m_minus"
            android:textColor="@color/colorFunctionText"
            android:textSize="12sp" />

        <!-- Row 1 -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnAC"
            style="@style/Widget.Material3.Button.TonalButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/ac"
            android:textColor="@color/colorACText"
            app:backgroundTint="@color/colorAC"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnC"
            style="@style/Widget.Material3.Button.TonalButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/c"
            android:textColor="@color/colorFunctionText"
            app:backgroundTint="@color/colorFunction"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMod"
            style="@style/Widget.Material3.Button.TonalButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/mod"
            android:textColor="@color/colorFunctionText"
            app:backgroundTint="@color/colorFunction"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnDivide"
            style="@style/Widget.Material3.Button.TonalButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/divide"
            android:textColor="@color/colorOperatorText"
            android:textSize="26sp"
            app:backgroundTint="@color/colorOperator"
            app:cornerRadius="16dp" />

        <!-- Row 2 -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn7"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/seven"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn8"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/eight"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn9"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/nine"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMultiply"
            style="@style/Widget.Material3.Button.TonalButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/multiply"
            android:textColor="@color/colorOperatorText"
            android:textSize="26sp"
            app:backgroundTint="@color/colorOperator"
            app:cornerRadius="16dp" />

        <!-- Row 3 -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn4"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/four"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn5"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/five"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn6"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/six"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMinus"
            style="@style/Widget.Material3.Button.TonalButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/minus"
            android:textColor="@color/colorOperatorText"
            android:textSize="26sp"
            app:backgroundTint="@color/colorOperator"
            app:cornerRadius="16dp" />

        <!-- Row 4 -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn1"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/one"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn2"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/two"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn3"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/three"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnAdd"
            style="@style/Widget.Material3.Button.TonalButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/plus"
            android:textColor="@color/colorOperatorText"
            android:textSize="26sp"
            app:backgroundTint="@color/colorOperator"
            app:cornerRadius="16dp" />

        <!-- Row 5 -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn0"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/zero"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnDot"
            style="@style/Widget.Material3.Button.ElevatedButton"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowWeight="1"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="@string/dot"
            android:textColor="@color/colorNumberText"
            app:backgroundTint="@color/colorNumber"
            app:cornerRadius="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnEqual"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_rowSpan="1"
            android:layout_rowWeight="1"
            android:layout_columnSpan="2"
            android:layout_columnWeight="2"
            android:layout_margin="6dp"
            android:text="@string/equal"
            android:textColor="@color/colorEqualText"
            android:textSize="26sp"
            app:backgroundTint="@color/colorEqual"
            app:cornerRadius="16dp" />

    </GridLayout>

</androidx.constraintlayout.widget.ConstraintLayout>
```

## OUTPUT

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e4dadae2-6e56-4518-8589-0aaa9b6c3ff5" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/460c7fe3-1693-45e7-bbbd-e7fc1f449031" />

## RESULT
Thus a Simple Android Application develop a program to create simple calculator in Android Studio is developed and executed successfully.
