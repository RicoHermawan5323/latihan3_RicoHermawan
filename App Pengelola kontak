package com.example.java;

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.*;
import java.io.*;
import java.sql.*;

public class PengelolaKontak extends JFrame {
    private JTextField inputNama, inputTelepon, inputCari;
    private JComboBox<String> kategoriPilihan;
    private JTable tabelKontak;
    private DefaultTableModel modelTabel;
    private Connection koneksi;

    public PengelolaKontak() {
        koneksiDatabase();

        setTitle("Pengelola Kontak");
        setSize(850, 650);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        JPanel panelInput = new JPanel(new GridLayout(4, 2, 10, 10));
        panelInput.setBorder(BorderFactory.createTitledBorder("Input Data"));
        panelInput.add(new JLabel("Nama:"));
        inputNama = new JTextField();
        panelInput.add(inputNama);

        panelInput.add(new JLabel("Nomor Telepon:"));
        inputTelepon = new JTextField();
        panelInput.add(inputTelepon);

        panelInput.add(new JLabel("Kategori:"));
        kategoriPilihan = new JComboBox<>(new String[]{"Keluarga", "Teman", "Kerja"});
        panelInput.add(kategoriPilihan);

        JButton tombolTambah = new JButton("Tambah");
        JButton tombolEdit = new JButton("Edit");
        JButton tombolHapus = new JButton("Hapus");
        panelInput.add(tombolTambah);
        panelInput.add(tombolEdit);
        panelInput.add(tombolHapus);

        JPanel panelPencarian = new JPanel(new FlowLayout());
        panelPencarian.setBorder(BorderFactory.createTitledBorder("Pencarian"));
        inputCari = new JTextField(25);
        JButton tombolCari = new JButton("Cari");
        panelPencarian.add(new JLabel("Cari:"));
        panelPencarian.add(inputCari);
        panelPencarian.add(tombolCari);

        modelTabel = new DefaultTableModel(new String[]{"ID", "Nama", "Telepon", "Kategori"}, 0);
        tabelKontak = new JTable(modelTabel);
        JScrollPane scrollPane = new JScrollPane(tabelKontak);

        JPanel panelEksporImpor = new JPanel(new FlowLayout());
        JButton tombolEkspor = new JButton("Ekspor ke CSV");
        JButton tombolImpor = new JButton("Impor dari CSV");
        panelEksporImpor.add(tombolEkspor);
        panelEksporImpor.add(tombolImpor);

        add(panelInput, BorderLayout.NORTH);
        add(scrollPane, BorderLayout.CENTER);
        add(panelPencarian, BorderLayout.SOUTH);
        add(panelEksporImpor, BorderLayout.PAGE_END);

        tombolTambah.addActionListener(e -> tambahKontak());
        tombolEdit.addActionListener(e -> editKontak());
        tombolHapus.addActionListener(e -> hapusKontak());
        tombolCari.addActionListener(e -> cariKontak());
        tombolEkspor.addActionListener(e -> eksporKeCSV());
        tombolImpor.addActionListener(e -> imporDariCSV());

        muatKontak();
    }

    private void koneksiDatabase() {
        try {
            koneksi = DriverManager.getConnection("jdbc:sqlite:kontak.db");
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Gagal terhubung ke database!", "Error", JOptionPane.ERROR_MESSAGE);
            System.exit(0);
        }
    }

    private void muatKontak() {
        try {
            modelTabel.setRowCount(0);
            Statement statement = koneksi.createStatement();
            ResultSet resultSet = statement.executeQuery("SELECT * FROM kontak");
            while (resultSet.next()) {
                modelTabel.addRow(new Object[]{
                        resultSet.getInt("id"),
                        resultSet.getString("nama"),
                        resultSet.getString("telepon"),
                        resultSet.getString("kategori")
                });
            }
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Gagal memuat data kontak!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void tambahKontak() {
        String nama = inputNama.getText();
        String telepon = inputTelepon.getText();
        String kategori = (String) kategoriPilihan.getSelectedItem();

        if (nama.isEmpty() || telepon.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Semua kolom harus diisi!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        try {
            PreparedStatement statement = koneksi.prepareStatement("INSERT INTO kontak (nama, telepon, kategori) VALUES (?, ?, ?)");
            statement.setString(1, nama);
            statement.setString(2, telepon);
            statement.setString(3, kategori);
            statement.executeUpdate();
            muatKontak();
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Gagal menambahkan kontak!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void editKontak() {
        int barisTerpilih = tabelKontak.getSelectedRow();
        if (barisTerpilih == -1) {
            JOptionPane.showMessageDialog(this, "Pilih kontak untuk diedit!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        int id = (int) modelTabel.getValueAt(barisTerpilih, 0);
        String nama = inputNama.getText();
        String telepon = inputTelepon.getText();
        String kategori = (String) kategoriPilihan.getSelectedItem();

        if (nama.isEmpty() || telepon.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Semua kolom harus diisi!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        try {
            PreparedStatement statement = koneksi.prepareStatement("UPDATE kontak SET nama = ?, telepon = ?, kategori = ? WHERE id = ?");
            statement.setString(1, nama);
            statement.setString(2, telepon);
            statement.setString(3, kategori);
            statement.setInt(4, id);
            statement.executeUpdate();
            muatKontak();
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Gagal mengedit kontak!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void hapusKontak() {
        int barisTerpilih = tabelKontak.getSelectedRow();
        if (barisTerpilih == -1) {
            JOptionPane.showMessageDialog(this, "Pilih kontak untuk dihapus!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        int id = (int) modelTabel.getValueAt(barisTerpilih, 0);

        try {
            PreparedStatement statement = koneksi.prepareStatement("DELETE FROM kontak WHERE id = ?");
            statement.setInt(1, id);
            statement.executeUpdate();
            muatKontak();
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Gagal menghapus kontak!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void cariKontak() {
        String keyword = inputCari.getText();
        try {
            modelTabel.setRowCount(0);
            PreparedStatement statement = koneksi.prepareStatement("SELECT * FROM kontak WHERE nama LIKE ? OR telepon LIKE ?");
            statement.setString(1, "%" + keyword + "%");
            statement.setString(2, "%" + keyword + "%");
            ResultSet resultSet = statement.executeQuery();
            while (resultSet.next()) {
                modelTabel.addRow(new Object[]{
                        resultSet.getInt("id"),
                        resultSet.getString("nama"),
                        resultSet.getString("telepon"),
                        resultSet.getString("kategori")
                });
            }
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Gagal mencari kontak!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void eksporKeCSV() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter("kontak.csv"))) {
            writer.write("ID,Nama,Telepon,Kategori\n");
            for (int i = 0; i < modelTabel.getRowCount(); i++) {
                writer.write(modelTabel.getValueAt(i, 0) + "," +
                        modelTabel.getValueAt(i, 1) + "," +
                        modelTabel.getValueAt(i, 2) + "," +
                        modelTabel.getValueAt(i, 3) + "\n");
            }
            JOptionPane.showMessageDialog(this, "Kontak berhasil diekspor!");
        } catch (IOException e) {
            JOptionPane.showMessageDialog(this, "Gagal mengekspor kontak!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void imporDariCSV() {
        try (BufferedReader reader = new BufferedReader(new FileReader("kontak.csv"))) {
            String line;
            reader.readLine();
            while ((line = reader.readLine()) != null) {
                String[] data = line.split(",");
                PreparedStatement statement = koneksi.prepareStatement("INSERT OR IGNORE INTO kontak (id, nama, telepon, kategori) VALUES (?, ?, ?, ?)");
                statement.setInt(1, Integer.parseInt(data[0]));
                statement.setString(2, data[1]);
                statement.setString(3, data[2]);
                statement.setString(4, data[3]);
                statement.executeUpdate();
            }
            muatKontak();
            JOptionPane.showMessageDialog(this, "Kontak berhasil diimpor!");
        } catch (IOException | SQLException e) {
            JOptionPane.showMessageDialog(this, "Gagal mengimpor kontak!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            PengelolaKontak app = new PengelolaKontak();
            app.setVisible(true);
        });
    }
}
