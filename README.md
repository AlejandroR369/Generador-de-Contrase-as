import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.time.LocalDateTime;
import java.util.Random;

/**
 * Clase que crea una interfaz gráfica para generar y gestionar contraseñas de usuarios.
 */
public class GeneradordeContraseñas extends JFrame {

    private JTextField campoUsuario;
    private JTextField campoLongitud;
    private JTextArea areaResultado;

    /**
     * Constructor de la clase GeneradordeContraseñas.
     * Configura la ventana principal, inicializa los componentes y define las acciones de los botones.
     */
    public GeneradordeContraseñas() {
        // Configuración de la ventana principal
        setTitle("Generador de Contraseñas");
        setSize(600, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // Panel superior para la entrada de datos
        JPanel panelEntrada = new JPanel(new GridLayout(3, 2));
        panelEntrada.add(new JLabel("Nombre de usuario:"));
        campoUsuario = new JTextField();
        panelEntrada.add(campoUsuario);
        panelEntrada.add(new JLabel("Longitud de la contraseña:"));
        campoLongitud = new JTextField();
        panelEntrada.add(campoLongitud);

        JButton botonGenerar = new JButton("Generar Contraseña");
        JButton botonVerListado = new JButton("Ver Usuarios y Contraseñas");
        JButton botonVerUsuarios = new JButton("Ver Usuarios");
        panelEntrada.add(botonGenerar);
        panelEntrada.add(botonVerListado);
        panelEntrada.add(botonVerUsuarios);

        // Área de texto para mostrar resultados
        areaResultado = new JTextArea(10, 50);
        areaResultado.setEditable(false);
        JScrollPane scrollResultado = new JScrollPane(areaResultado);

        // Añadir los paneles a la ventana
        add(panelEntrada, BorderLayout.NORTH);
        add(scrollResultado, BorderLayout.CENTER);

        // Acción al presionar el botón "Generar Contraseña"
        botonGenerar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String usuario = campoUsuario.getText();
                String longitudTexto = campoLongitud.getText();

                if (!usuario.isEmpty() && !longitudTexto.isEmpty()) {
                    try {
                        int longitud = Integer.parseInt(longitudTexto);
                        String contrasena = generarContrasena(longitud);
                        String fechaHora = LocalDateTime.now().toString();
                        guardarUsuarioYContrasena(usuario, contrasena, fechaHora);
                        guardarUsuario(usuario);
                        areaResultado.setText("Contraseña generada para " + usuario + ": " + contrasena);
                    } catch (NumberFormatException ex) {
                        JOptionPane.showMessageDialog(null, "La longitud debe ser un número entero.");
                    }
                } else {
                    JOptionPane.showMessageDialog(null, "Por favor, ingrese el nombre de usuario y la longitud de la contraseña.");
                }
            }
        });

        // Acción al presionar el botón "Ver Usuarios y Contraseñas"
        botonVerListado.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                mostrarUsuariosYContrasenas();
            }
        });

        // Acción al presionar el botón "Ver Usuarios"
        botonVerUsuarios.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                mostrarUsuarios();
            }
        });
    }

    /**
     * Genera una contraseña aleatoria de la longitud especificada.
     *
     * @param longitud Longitud de la contraseña a generar.
     * @return La contraseña generada.
     */
    private String generarContrasena(int longitud) {
        String caracteres = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*()";
        Random random = new Random();
        StringBuilder contrasena = new StringBuilder(longitud);
        for (int i = 0; i < longitud; i++) {
            int indice = random.nextInt(caracteres.length());
            contrasena.append(caracteres.charAt(indice));
        }
        return contrasena.toString();
    }

    /**
     * Guarda el nombre de usuario y la contraseña generada en un archivo.
     *
     * @param usuario   Nombre de usuario.
     * @param contrasena Contraseña generada.
     * @param fechaHora Fecha y hora de la generación.
     */
    private void guardarUsuarioYContrasena(String usuario, String contrasena, String fechaHora) {
        try {
            File folder = new File("txt");
            if (!folder.exists()) {
                folder.mkdir();
            }
            FileWriter writer = new FileWriter("txt/usuarios_contrasenas.txt", true);
            writer.write("Usuario: " + usuario + ", Contraseña: " + contrasena + ", Fecha y Hora: " + fechaHora + "\n");
            writer.close();
        } catch (IOException e) {
            JOptionPane.showMessageDialog(this, "Error al guardar en el archivo de contraseñas.");
        }
    }

    /**
     * Guarda el nombre de usuario en un archivo.
     *
     * @param usuario Nombre de usuario a guardar.
     */
    private void guardarUsuario(String usuario) {
        try {
            File folder = new File("txt");
            if (!folder.exists()) {
                folder.mkdir();
            }
            FileWriter writer = new FileWriter("txt/usuarios.txt", true);
            writer.write("Usuario: " + usuario + "\n");
            writer.close();
        } catch (IOException e) {
            JOptionPane.showMessageDialog(this, "Error al guardar en el archivo de usuarios.");
        }
    }

    /**
     * Muestra el listado de usuarios y contraseñas en el área de resultados.
     */
    private void mostrarUsuariosYContrasenas() {
        try {
            areaResultado.setText("");
            java.nio.file.Files.lines(java.nio.file.Paths.get("txt/usuarios_contrasenas.txt"))
                .forEach(linea -> areaResultado.append(linea + "\n"));
        } catch (IOException e) {
            JOptionPane.showMessageDialog(this, "Error al leer el archivo de contraseñas.");
        }
    }

    /**
     * Muestra el listado de usuarios en el área de resultados.
     */
    private void mostrarUsuarios() {
        try {
            areaResultado.setText("");
            java.nio.file.Files.lines(java.nio.file.Paths.get("txt/usuarios.txt"))
                .forEach(linea -> areaResultado.append(linea + "\n"));
        } catch (IOException e) {
            JOptionPane.showMessageDialog(this, "Error al leer el archivo de usuarios.");
        }
    }

    /**
     * Método principal que inicia la aplicación.
     *
     * @param args Argumentos de línea de comandos.
     */
    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            GeneradordeContraseñas ventana = new GeneradordeContraseñas();
            ventana.setVisible(true);
        });
    }
}
