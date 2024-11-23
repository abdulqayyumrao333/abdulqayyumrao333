package com.example.myfantasygame;

import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Rect;
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    
    // Game variables
    private GameView gameView;
    private PlayerCharacter player;
    private WeatherEffect weatherEffect;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // Initialize the Game View and Player
        gameView = new GameView(this);
        setContentView(gameView);
        
        // Initialize the player character
        player = new PlayerCharacter();
        player.setPosition(100, 100);
        
        // Initialize weather effect
        weatherEffect = new WeatherEffect();
    }
    
    // Game View
    public class GameView extends android.view.View {
        
        private Bitmap background;
        private Paint paint;
        
        public GameView(MainActivity context) {
            super(context);
            paint = new Paint();
            background = Bitmap.createBitmap(1920, 1080, Bitmap.Config.ARGB_8888);
        }

        @Override
        protected void onDraw(Canvas canvas) {
            super.onDraw(canvas);
            
            // Draw the background
            canvas.drawBitmap(background, new Rect(0, 0, background.getWidth(), background.getHeight()), new Rect(0, 0, getWidth(), getHeight()), paint);
            
            // Update and render the weather effect (Rain)
            weatherEffect.update();
            weatherEffect.render(canvas, paint);
            
            // Draw the player character
            player.render(canvas, paint);
            
            // Add any other game logic, like updating animations, sounds, etc.
            
            // Request the next frame
            invalidate();
        }
    }

    // PlayerCharacter Class
    public class PlayerCharacter {
        private float x, y;
        private Bitmap[] walkingFrames;
        private int currentFrame = 0;
        private long lastUpdate = 0;
        
        public PlayerCharacter() {
            // Load walking frames for the character
            walkingFrames = new Bitmap[4]; // For example, we have 4 frames for walking
            // Load bitmaps here (replace with actual loading logic)
        }
        
        public void setPosition(float x, float y) {
            this.x = x;
            this.y = y;
        }
        
        public void animate() {
            long now = System.currentTimeMillis();
            if (now - lastUpdate > 100) { // Update every 100ms
                currentFrame = (currentFrame + 1) % walkingFrames.length;
                lastUpdate = now;
            }
        }
        
        public void render(Canvas canvas, Paint paint) {
            animate();
            // Draw the current walking frame at the player's position
            canvas.drawBitmap(walkingFrames[currentFrame], x, y, paint);
        }
    }
    
    // WeatherEffect (Rain) Class
    public class WeatherEffect {
        private ArrayList<RainDrop> rainDrops;
        
        public WeatherEffect() {
            rainDrops = new ArrayList<>();
            // Create a number of raindrops
            for (int i = 0; i < 100; i++) {
                rainDrops.add(new RainDrop(Math.random() * getWidth(), Math.random() * getHeight()));
            }
        }

        public void update() {
            for (RainDrop drop : rainDrops) {
                drop.update();
                // Reset raindrop position if it goes out of screen
                if (drop.getY() > getHeight()) {
                    drop.setY(0);
                    drop.setX(Math.random() * getWidth());
                }
            }
        }

        public void render(Canvas canvas, Paint paint) {
            paint.setColor(Color.BLUE);
            for (RainDrop drop : rainDrops) {
                canvas.drawCircle(drop.getX(), drop.getY(), 5, paint);
            }
        }
    }

    // RainDrop Class for Weather Effect
    class RainDrop {
        private float x, y, speed;
        
        public RainDrop(double x, double y) {
            this.x = (float) x;
            this.y = (float) y;
            this.speed = (float) (Math.random() * 10 + 5); // Random speed for variation
        }

        public void update() {
            this.y += speed; // Move the raindrop downward
        }

        public float getX() { return x; }
        public float getY() { return y; }

        public void setX(float x) { this.x = x; }
        public void setY(float y) { this.y = y; }
    }
}
