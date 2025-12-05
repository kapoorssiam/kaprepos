# kaprepos
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

/**
 * Bouncing Ball Animation
 * A simple Java Swing animation demonstrating a bouncing 123 ball with physics simulation
 */
public class BouncingBallAnimation extends JPanel implements ActionListener {
    
    // Animation properties
    private final int WIDTH = 800;
    private final int HEIGHT = 600;
    private final int BALL_SIZE = 50;
    private final int DELAY = 16; // ~60 FPS
    
    // Ball position and velocity
    private int ballX = 100;
    private int ballY = 100;
    private int velocityX = 5;
    private int velocityY = 4;
    
    // Physics properties
    private final double GRAVITY = 0.5;
    private final double BOUNCE_DAMPING = 0.8;
    
    // Colors
    private final Color BACKGROUND_COLOR = new Color(240, 248, 255);
    private final Color BALL_COLOR = new Color(70, 130, 180);
    private final Color SHADOW_COLOR = new Color(0, 0, 0, 50);
    
    private Timer timer;

    /**
     * Constructor - Initializes the animation panel
     */
    public BouncingBallAnimation() {
        setPreferredSize(new Dimension(WIDTH, HEIGHT));
        setBackground(BACKGROUND_COLOR);
        
        // Initialize animation timer
        timer = new Timer(DELAY, this);
        timer.start();
    }

    /**
     * Main painting method - Renders the ball and shadow
     */
    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        Graphics2D g2d = (Graphics2D) g;
        
        // Enable anti-aliasing for smoother graphics
        g2d.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
        
        // Draw shadow
        g2d.setColor(SHADOW_COLOR);
        g2d.fillOval(ballX + 5, ballY + 10, BALL_SIZE, BALL_SIZE / 3);
        
        // Draw ball with gradient for 3D effect
        GradientPaint gradient = new GradientPaint(
            ballX, ballY, BALL_COLOR.brighter(),
            ballX + BALL_SIZE, ballY + BALL_SIZE, BALL_COLOR.darker()
        );
        g2d.setPaint(gradient);
        g2d.fillOval(ballX, ballY, BALL_SIZE, BALL_SIZE);
        
        // Draw ball outline
        g2d.setColor(Color.BLACK);
        g2d.drawOval(ballX, ballY, BALL_SIZE, BALL_SIZE);
    }

    /**
     * Animation loop - Updates ball position and physics
     */
    @Override
    public void actionPerformed(ActionEvent e) {
        // Update position with velocity
        ballX += velocityX;
        ballY += velocityY;
        
        // Apply gravity
        velocityY += GRAVITY;
        
        // Check wall collisions
        if (ballX <= 0 || ballX + BALL_SIZE >= WIDTH) {
            velocityX = (int) (-velocityX * BOUNCE_DAMPING);
            ballX = Math.max(0, Math.min(ballX, WIDTH - BALL_SIZE));
        }
        
        if (ballY <= 0 || ballY + BALL_SIZE >= HEIGHT) {
            velocityY = (int) (-velocityY * BOUNCE_DAMPING);
            ballY = Math.max(0, Math.min(ballY, HEIGHT - BALL_SIZE));
            
            // Add some horizontal friction when bouncing on ground
            if (ballY + BALL_SIZE >= HEIGHT) {
                velocityX *= 0.95;
            }
        }
        
        // Prevent ball from getting stuck
        if (Math.abs(velocityX) < 0.5) velocityX = 0;
        if (Math.abs(velocityY) < 0.5 && ballY + BALL_SIZE >= HEIGHT - 1) {
            velocityY = 0;
        }
        
        // Redraw the animation
        repaint();
    }

    /**
     * Main method - Creates and displays the animation window
     */
    public static void main(String[] args) {
        // Create and setup the frame
        JFrame frame = new JFrame("Bouncing Ball Animation");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setResizable(false);
        
        // Add animation panel to frame
        BouncingBallAnimation animation = new BouncingBallAnimation();
        frame.add(animation);
        frame.pack();
        frame.setLocationRelativeTo(null); // Center the window
        frame.setVisible(true);
        
        System.out.println("Bouncing Ball Animation Started!");
        System.out.println("Ball follows physics with gravity and bouncing effects");
    }
}
