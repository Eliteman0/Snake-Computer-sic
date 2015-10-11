package Snakethegame.Grahpics;

import java.awt.Color;
import java.awt.Dimension;
import java.awt.Graphics;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.util.ArrayList;
import java.util.Random;

import javax.swing.JPanel;

import Snakethegame.Entities.Apple;
import Snakethegame.Entities.BodyPart;

public class Screen extends JPanel implements Runnable{

	public static final int WIDTH = 800, HEIGHT = 800;
	private Thread thread;
	private boolean running = false;
	
	private BodyPart b;
	private ArrayList<BodyPart> snake;
	
	private Random r;
	
	//apple
	private Apple apple;
	private ArrayList<Apple> apples;
	
	//head placement
	private int xCoor = 10, yCoor = 10;
	private int size = 5;
	
	//starting direction
	private boolean right = true,left = false, up = false, down = false;
	
	private int ticks = 0;
	
	private Key key;
	
	
	public Screen(){
setFocusable(true);
key = new Key();
addKeyListener(key);
		setPreferredSize(new Dimension(WIDTH, HEIGHT));
		
		r = new Random();
		
		snake = new ArrayList<BodyPart>();
		apples = new ArrayList<Apple>();
		
		
		//Start the game straight away
		start();
	}
	public void tick(){
	if(snake.size() == 0){
		b = new BodyPart(xCoor, yCoor, 10);
		snake.add(b);
	}
	//This spawn in the apple and add the random affect to it as well
	if(apples.size() == 0){
		int xCoor = r.nextInt(79);
		int YCoor = r.nextInt(79);
		
		apple = new Apple(xCoor, yCoor, 10);
		apples.add(apple);
	}
	//This make the program go thought the ArrayList and makes it loop by using a for loop
	for(int i = 0; i <apples.size(); i++){
		if(xCoor == apples.get(i).getxCoor() && yCoor == apples.get(i).getxCoor()){
			size++;
			apples.remove(i);
			i--;
		}
	}
	for(int i = 0; i < snake.size(); i++){
		if(xCoor == snake.get(i).getxCoor() && yCoor == snake.get(i).getyCoor()){
			if(i != snake.size() - 1) {
				stop();
			}
		}
	}
	if(xCoor < 0 || xCoor >79 || yCoor <0 || yCoor >79){
		 if(xCoor < 0) xCoor = 79;
		 if(xCoor > 79) xCoor = 0;
		 if(yCoor < 0) yCoor = 79;
		 if(yCoor > 79) yCoor = 0;
	}
	
	
	
	//stop it from updating to often
	ticks++;
	if(ticks>200000){
		if(right) xCoor++;
		if(left) xCoor--;
		if(down) yCoor++;
		if(up) yCoor--;
		//my it start over
		ticks=0;
		//add new body parts
		b = new BodyPart(xCoor,yCoor,10);
		snake.add(b);
	
		
		if(snake.size()>size){
		snake.remove(0);
   }
		
 }
}
	   public void paint(Graphics g){
	   //clear not want snake parts
		   g.clearRect(0, 0, WIDTH, HEIGHT);
		//Grid
		g.setColor(Color.BLACK);
		for(int i=0; i< WIDTH / 10; i++){
		g.drawLine(i+10,0,i*10,HEIGHT);
	
		}
		for(int i=0; i< HEIGHT / 10; i++){
		g.drawLine(0,i+10,WIDTH,i*10);
		
		}
        //Make the Arry loop work
		for(int i=0; i < snake.size(); i++){
        snake.get(i).draw(g);
		}
		for(int i = 0; i <apples.size(); i++){
		apples.get(i).draw(g);
		}
	   }

		public void start(){
		running = true;
		thread = new Thread(this, "game Loop");
		thread.start();
	}
	public void stop(){
	running = false;
	try {
		thread.join();
	} catch (InterruptedException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
	}
	public void run(){
		while (running){
			tick();
			repaint();
		}
	
	}
	private class Key implements KeyListener{

		@Override
		public void keyPressed(KeyEvent e) {
			int key = e.getKeyCode();
		
if(key == KeyEvent.VK_RIGHT && !left){
up = false;
  down = false;
  right = true;
  
			}
if(key == KeyEvent.VK_LEFT && !right){
 up = false;
  down = false;
 left = true;
			}		
if(key == KeyEvent.VK_UP && !down){
  up = true;
  down = false;
  right = false;
  left = false;
			}
if(key == KeyEvent.VK_DOWN && !up){
down = true;
 right = false;
 left = false;
			}
			
		}

	
		public void keyReleased(KeyEvent arg0) {
		}
       public void keyTyped(KeyEvent arg0) {
		}
		
	}
	}

