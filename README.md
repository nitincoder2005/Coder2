import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.*;
import java.io.*;

public class adv extends JFrame implements ActionListener {

    JTextField nameField;
    JButton addBtn, presentBtn, absentBtn, deleteBtn, saveBtn;
    JTable table;
    DefaultTableModel model;

    public adv() {

        setTitle("Advanced Attendance System");
        setSize(700, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // TOP PANEL
        JPanel topPanel = new JPanel();

        topPanel.add(new JLabel("Student Name:"));
        nameField = new JTextField(15);
        topPanel.add(nameField);

        addBtn = new JButton("Add Student");
        presentBtn = new JButton("Mark Present");
        absentBtn = new JButton("Mark Absent");
        deleteBtn = new JButton("Delete");
        saveBtn = new JButton("Save");

        topPanel.add(addBtn);
        topPanel.add(presentBtn);
        topPanel.add(absentBtn);
        topPanel.add(deleteBtn);
        topPanel.add(saveBtn);

        add(topPanel, BorderLayout.NORTH);

        // TABLE
        model = new DefaultTableModel();
        model.addColumn("Student Name");
        model.addColumn("Present");
        model.addColumn("Absent");
        model.addColumn("Attendance %");

        table = new JTable(model);
        add(new JScrollPane(table), BorderLayout.CENTER);

        // BUTTON ACTIONS
        addBtn.addActionListener(this);
        presentBtn.addActionListener(this);
        absentBtn.addActionListener(this);
        deleteBtn.addActionListener(this);
        saveBtn.addActionListener(this);

        loadData(); // load saved data

        setVisible(true);
    }

    public void actionPerformed(ActionEvent e) {

        if (e.getSource() == addBtn) {

            String name = nameField.getText();

            if (name.isEmpty()) {
                JOptionPane.showMessageDialog(this, "Enter student name!");
                return;
            }

            model.addRow(new Object[]{name, 0, 0, "0%"});
            nameField.setText("");
        }

        int selectedRow = table.getSelectedRow();

        if (selectedRow == -1) return;

        int present = Integer.parseInt(model.getValueAt(selectedRow, 1).toString());
        int absent = Integer.parseInt(model.getValueAt(selectedRow, 2).toString());

        if (e.getSource() == presentBtn) {
            present++;
        }

        if (e.getSource() == absentBtn) {
            absent++;
        }

        int total = present + absent;
        double percent = (present * 100.0) / total;

        model.setValueAt(present, selectedRow, 1);
        model.setValueAt(absent, selectedRow, 2);
        model.setValueAt(String.format("%.2f%%", percent), selectedRow, 3);

        if (e.getSource() == deleteBtn) {
            model.removeRow(selectedRow);
        }

        if (e.getSource() == saveBtn) {
            saveData();
        }
    }

    // SAVE DATA TO FILE
    private void saveData() {
        try {
            BufferedWriter bw = new BufferedWriter(new FileWriter("attendance.txt"));

            for (int i = 0; i < model.getRowCount(); i++) {

                bw.write(model.getValueAt(i, 0) + "," +
                        model.getValueAt(i, 1) + "," +
                        model.getValueAt(i, 2) + "," +
                        model.getValueAt(i, 3));

                bw.newLine();
            }

            bw.close();
            JOptionPane.showMessageDialog(this, "Data Saved!");

        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    // LOAD DATA
    private void loadData() {

        File file = new File("attendance.txt");

        if (!file.exists()) return;

        try {
            BufferedReader br = new BufferedReader(new FileReader(file));
            String line;

            while ((line = br.readLine()) != null) {

                String[] data = line.split(",");
                model.addRow(data);
            }

            br.close();

        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    public static void main(String[] args) {
        new adv();
    }
}

