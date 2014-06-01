package SchedulingSimulator;

import java.io.File;
import java.io.FileNotFoundException;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.Scanner;


//Unlike Round robin I did not use a timer for this program, it will just output all at once the entire
//Gantt chart and wait/turnaround chart
public class SRTFScheduler {

	private LinkedList<process> arrivalGroup= new LinkedList<process>();
	private LinkedList<process> readyQueue = new LinkedList<process>();
	private process onCPU[] = new process[1];
	private process completed[] = new process[8];
	private int processesCompleted = 0;
	
	//uses same code as RoundRobin to load process' from file into arrival queue
	public SRTFScheduler(String fileName) {
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
	
	//same as RoundRobin, but will check readyQueue for shortest job
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
		shortestJob(onCPU[0]);
		

	}
	
	//will scan readyQueue for shortest cpu burst process, remove it from readyQueue and return it
	public boolean shortestJob(process current){
		int currShortest = current.getBurst();
		process examine;
		process shortest = null;
		boolean firstRun = true;
		
		//run through readyQueue to find shortest process 
		Iterator<process> iter = readyQueue.listIterator();
		while(iter.hasNext()){
			if(firstRun){
				shortest = iter.next();
				firstRun = false;
			}
			else{
				examine = iter.next();
				if(examine.getBurst() < shortest.getBurst()){
					shortest = examine;
				}
			}
		}
		//check if job is shorter than current cpu process
		if(shortest.getBurst() < current.getBurst()){
		readyQueue.remove(shortest);
		readyQueue.addLast(onCPU[0]);
		onCPU[0] = shortest;
		return true;
		}
		else{
			return false;
		}
	}
	
	
	//This is a modified version of shortestJob function above that runs after a cpu process terminates
	//dont need to compare readyqueue to cpu process in this one
	public process shortestJob2(){
		process examine;
		process shortest = null;
		boolean firstRun = true;
		
		//run through readyQueue to find shortest process 
		Iterator<process> iter = readyQueue.listIterator();
		while(iter.hasNext()){
			if(firstRun){
				shortest = iter.next();
				firstRun = false;
			}
			else{
				examine = iter.next();
				if(examine.getBurst() < shortest.getBurst()){
					shortest = examine;
				}
			}
		}
		readyQueue.remove(shortest);
		return shortest;
	}
	
	
	//simulator loop:
	//1.check current process for termination,if it is move to completed and move shortest ready queue job in
	//2.check if shorter process in ready Queue
	//3.reduce cpu burst of cpu process, increase wait/turnaround of readyqueue processes
	//decrease arrival time of arriving process'
	//
	public void startSimulator(){
		checkReady();
		System.out.print("SRTF Scheduling");
		int timePassed = 0;
		boolean eightDone = false;
		while(processesCompleted < 8){
			if(onCPU[0].isComplete()){
				System.out.print("\n" + timePassed +" " + onCPU[0].getName() + " Process Terminated");
				completed[processesCompleted]=onCPU[0];
				processesCompleted++;
				onCPU[0]=shortestJob2();
				if(processesCompleted == 8){
					eightDone = true;
				}
			}
			else if(processesCompleted >= 6){
				
			}
			else{
				if(shortestJob(onCPU[0])){
				System.out.print("\n" + timePassed + " " + onCPU[0].getName() + " Process Preempted");
				}
			}
			if(!eightDone){
			//run cpu process
				onCPU[0].cpuRun();
			
			
				//increase waitTime/turnaround time of readyqueue processes
				Iterator<process> iter = readyQueue.listIterator();
				while(iter.hasNext()){
					iter.next().minusTime();
				
				}
				
			
				//move arriving processes closer to arrival
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
			
			}
			timePassed++;
		}
		
		printStatistics();
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
