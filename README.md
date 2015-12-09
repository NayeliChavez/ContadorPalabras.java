import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Scanner;
import java.util.Set;
import java.util.TreeMap;
import java.util.TreeSet;
import javax.lang.model.element.Element;

public class Contador {
	
	public static List<HashMap<String,String>> Contador(String file){
		
		List<HashMap<String,String>> data = new ArrayList<HashMap<String,String>>();
		Scanner sc = null;
		int contP=0;
		
		//********** OBTENER LAS PALABRAS DEL ARCHIVO STOPWORDS **********//
		String archivo = "stopwords.txt";
		List<String> palabrasExc = new ArrayList<String>();
		
		BufferedReader br = null;
		try{
			br = new BufferedReader(new FileReader(archivo));
			String currentLine;	
			while((currentLine = br.readLine()) != null){	//mostrar los datos del archivo stopwords hasta encontrar null
				palabrasExc.add(currentLine);
			}
		}
		catch(FileNotFoundException e){
			System.out.println("El archivo no existe");
		}catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			System.out.println(" ");
			if(br != null){
				try{
					br.close();
				}catch(IOException e){
					e.printStackTrace();
				}
			}
		}
	
		//********** OBTENER LAS PALABRAS DEL ARCHIVO DATA **********//
		try{		
			//********** CONTAR EL NUMERO DE PALABRAS EN EL TEXTO **********//
			sc = new Scanner(new File(file));
			sc.useDelimiter(",|\\.|;|-|\\p{javaWhitespace}+");
			
			HashMap<String,Integer> contadorWord = new HashMap<String,Integer>();

			ValueComparator comp = new ValueComparator(contadorWord);
			TreeMap<String,Integer> sorted = new TreeMap<String,Integer>(comp);
			
			//ver si existe una cadena
			while(sc.hasNext()){	
				String word = sc.next().trim().toLowerCase();	
				if(!word.equals("")){	//si no es una cadena vacia, se imprime la palabra
					contP++;
					
					if(contadorWord.containsKey(word)){		//ver si la palabra esta en el HashMap
						
						if(!contadorWord.containsKey(palabrasExc)){
							int cont = contadorWord.get(word);	//obtener la cuenta actual
							contadorWord.put(word, cont+1);		//incrementa la cuenta
						}
					}else{
						contadorWord.put(word, 1);		//agrega una nueva palabra y el contador inicializa en uno
					}
				}
			}
			System.out.println("TOTAL DE PALABRAS: " + contP + "\n");
			
			//********** GUARDAR LOS DATOS EN UN ARCHIVO ********** //
			BufferedWriter bw = null;			
			try{
				bw = new BufferedWriter(new FileWriter("resumen.txt"));
				
				bw.write("===== Total de palabras =====");
				bw.write(contP + "\n");
				
				//********** MOSTRAR LAS PALABRAS Y EL NUMERO DE VECES QUE SE REPITE ********** //
				
				Set<String> totalRep = contadorWord.keySet();		//guarda el numero de veces que se repite cada palabra
				TreeSet<String> ordenarTotal = new TreeSet<String>(totalRep);
				
				System.out.println("NUMERO DE REPETICIONES POR PALABRA \n");
				for(String palabra: ordenarTotal){
					System.out.println(palabra + "\t" + contadorWord.get(palabra));
				}
				

				//********** ORDENAR LOS VALORES DEL HASHMAP DE MAYOR A MAYOR **********//
				System.out.println("===== PALABRAS ORDENADAS DE MAYOR A MENOR =====");
				sorted.putAll(contadorWord);
				System.out.println(sorted);
				
				bw.write("===== PALABRAS ORDENADAS DE MAYOR A MENOR =====");
				bw.write(sorted + "\n");

				//********** IMPRIMIR LOS DIEZ PRIMEROS VALORES **********//

				bw.write("===== IMPRIMIR LOS DIEZ PRIMEROS VALORES =====");
				int val=1;
				System.out.println("Diez palabras mas repetidas");
				for(String key: contadorWord.keySet()){
					if(val<=10){
						System.out.println("Palabra: " + key);
						val++;
						bw.write(key);
					}
				}
				
			}catch(IOException e1){
				System.out.println("No se pudo crear el archivo");
			}finally{
				if(bw != null){
					try{
						bw.close();
					}catch(IOException e){
						e.printStackTrace();
					}
				}
			}	
		}catch(FileNotFoundException e){
				
		}
		return data;	
	}
		
	public static void main(String[] args){
		Contador("data.txt");	
	}

}


// CLASE VALUECOMPARATOR //
import java.util.Comparator;
import java.util.HashMap;
import java.util.Map;

public class ValueComparator implements Comparator<String>{
	
	 Map<String,Integer> base;
	    public ValueComparator(HashMap<String, Integer> contadorWord) {
	        this.base = contadorWord;
	    }
    
	    public int compare(String a, String b) {
	        if (base.get(a) >= base.get(b)) {
	            return -1;
	        } else {
	            return 1;
	        } 
	    }

}
