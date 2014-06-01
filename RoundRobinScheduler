package SchedulingSimulator;
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Iterator;
import java.util.ListIterator;
import java.util.Scanner;
import java.util.Timer;
import java.util.LinkedList;
import java.util.TimerTask;


//I did a timer for this one so it would print out the current process each second rather 
//than just print out everything all at once
public class RoundRobinScheduler {

	private LinkedList<process> arrivalGroup= new LinkedList<process>();
	private LinkedList<process> readyQueue = new LinkedList<process>();
	private process onCPU[] = new process[1];
	private process completed[] = new process[8];
	Timer timer = new Timer();
	int currentQuantum = 3;
	int timePassed = 0;
	int completedProcesses = 0;
	
	//constructor loads all the information from file into 8 processes which it puts in the arrival group
	public RoundRobinScheduler(String fileName) {
		Scanner inputs = null;
		try {
			inputs = new Scanner(new File(fileName));
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		int processNumber = 0; //this will keep track of what process we are reading in
		int inputNumber = 1; //this will moniter when all 3 inputs have been read in to the process 
		String processName = null;
		int processBurst = 0;
		int processArrival = 0;
		while (inputs.hasNextLine()){
			Scanner temp = new Scanner(inputs.nextLine());
		
			//process the current line which is one process' details, then put it into arrivalGroup
			while(temp.hasNext()){
				String nextValue = temp.next();
				if(inputNumber == 1){
					processName = nextValue;
					inputNumber++;
				}
				else if(inputNumber == 2){
					processBurst = Integer.parseInt(nextValue);

					inputNumber++;
				}
				else if(inputNumber == 3){
					processArrival = Integer.parseInt(nextValue);
					

					inputNumber = 1;
				}
				
			}
			arrivalGroup.addLast(new process(processName,processBurst,processArrival));
			processNumber++;

			
		}
	}
	
	//this will run once at start to load processes from arrival to ready/cpu
	public void checkReady(){
		Iterator<process> iter = arrivalGroup.listIterator();
		while(iter.hasNext()){
			process temp = iter.next();
			if(temp.isReady()){
				iter.remove();
				readyQueue.addLast(temp);
			}
		}
		onCPU[0] = readyQueue.removeFirst();

	}
	
	
	//will run simulator, calling oneSecond() function every second 
	public void startSimulator(){
		checkReady();
		System.out.println("Round Robin");
		timer.scheduleAtFixedRate(new TimerTask(){

			@Override
			public void run() {
				oneSecond();
				
				
			}
			
		}, 1000,1000);
	}
	
	
	
	
	//This is the main process that will simulate the RR actions:
	
	//1.check if all tasks completed, if they are then end simulator and call statistics print method.
	//2.check if quantum expired, if it is push task back to readyqueue and get next task onto
	//cpu, print out "quantum expired"
	//3.check if previous task completed, if it is move it to completed array, get next task onto cpu 
	//print out statistics
	//4. run the minusTime on readyQueue and arrival array
	//5. run onCPU on the cpu process
	public void oneSecond(){
		if(completedProcesses >= 8){
			timer.cancel();
			printStatistics();
			return;
		}
		System.out.print("\n"+timePassed + " " + onCPU[0].getName() + " cpu burst:" + onCPU[0].getBurst());
	
		
		if(onCPU[0].isComplete()){
			System.out.print(" Process terminated");
			completed[completedProcesses] = onCPU[0];
			completedProcesses++;
			if(completedProcesses == 8){
				currentQuantum = 1;
			}
		
			else{
			onCPU[0] = readyQueue.removeFirst();
			
			currentQuantum = 3;
			
			}
		
		}
		
		if(currentQuantum == 0){
			System.out.print(" Quantum expired");
			readyQueue.addLast(onCPU[0]);
			onCPU[0] = readyQueue.removeFirst();
			currentQuantum = 3;
			
		}
		
		//run minus time on readyQueue
		Iterator<process> iter = readyQueue.listIterator();
		while(iter.hasNext()){
			iter.next().minusTime();
			
		}
		
		//run minus time on arriving process'
		Iterator<process> iter2 = arrivalGroup.listIterator();
		while(iter2.hasNext()){
			iter2.next().minusTime();
		}
		
		//move arriving processes to readyqueue if they arrive
		Iterator<process> iter3 = arrivalGroup.listIterator();
		while(iter3.hasNext()){
			process temps = iter3.next();
			if(temps.isReady()){
				readyQueue.addLast(temps);
				iter3.remove();
			}
		}
		
		
		//run cpu process
		onCPU[0].cpuRun();
			
		timePassed++;	
		currentQuantum--;
	}

	public void printStatistics(){
		System.out.println("\n\nPROCESS ID|WAIT TIME|TURNAROUND TIME");
		for(process P: completed){
			P.print();
			
		}
		System.out.println("AVERAGES: "+ avgWait()+"       "+avgTurnaround());
	}
	
	public float avgTurnaround(){
		float average = 0;
		for(process P: completed){
			average = average + P.getTurnaround();
		}
		return average/8;
	}
	
	
	public float avgWait(){
		float average = 0;
		for(process P: completed){
			average = average + P.getWait();
		}
		return average/8;
	}
	
	
}
