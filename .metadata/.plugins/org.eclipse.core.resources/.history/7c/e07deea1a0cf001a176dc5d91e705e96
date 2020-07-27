package CompiladorAnalizar;
import java.io.*;
import java.util.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import javax.swing.JOptionPane;
 
public class Analizar 
{
	int renglon=1;
	ArrayList<String> impresion; 
	ArrayList<Identificador> identi = new ArrayList<Identificador>();
	ListaDoble<Token> tokens;
	ArrayList<String> aux = new ArrayList<String>();
	final Token vacio=new Token("", 9,0);
	boolean bandera=true;
	ArrayList<Arbol> arbol = new ArrayList<Arbol>();
	ArrayList<String> expresion = new ArrayList<String>();

	public ArrayList<Identificador> getIdenti() {
		return identi;
	}
	public ArrayList<Arbol> getIdenti2() {
		return arbol ;
	}
	public Analizar(String ruta) {//Recibe el nombre del archivo de texto
		analisaCodigo(ruta);
		if(bandera) {
			impresion.add("No hay errores lexicos");
			analisisSintactico(tokens.getInicio());
		}
		if(impresion.get(impresion.size()-1).equals("No hay errores lexicos"))
			impresion.add("No hay errores sintacticos");
			analisisSemantico(tokens.getInicio());
	}
	//Entrada de Codigo y analiza
	public void analisaCodigo(String ruta) {
		String linea="", token="";
		StringTokenizer tokenizer;
		try{
	          FileReader file = new FileReader(ruta);
	          BufferedReader archivoEntrada = new BufferedReader(file);
	          linea = archivoEntrada.readLine();
	          impresion=new ArrayList<String>();
	          tokens = new ListaDoble<Token>();
	          while (linea != null){
	        	    linea = separaDelimitadores(linea);
	                tokenizer = new StringTokenizer(linea);
	                while(tokenizer.hasMoreTokens()) {
	                	token = tokenizer.nextToken();
	                	analisisLexico(token);
	                }
	                linea=archivoEntrada.readLine();
	                renglon++;
	          }
	          archivoEntrada.close();
		}catch(IOException e) {
			JOptionPane.showMessageDialog(null,"No se encontro el archivo favor de checar la ruta","Alerta Chavo",JOptionPane.ERROR_MESSAGE);
		}
	}
	//AnalisisSintactico
	public Token analisisSintactico(NodoDoble<Token> nodo) {
		Token  to;
		if(nodo!=null) // por si acaso :b
		{
			to =  nodo.dato;
			
			switch (to.getTipo())
			{
			case Token.MODIFICADOR:
				int sig=nodo.siguiente.dato.getTipo();
				 
				if(sig!=Token.TIPO_DATO && sig!=Token.CLASE)
					impresion.add("Error sinatactico en la linea "+to.getLinea()+" se esparaba un tipo de dato");
				break;
			case Token.IDENTIFICADOR:
				// lo que puede seguir despues de un idetificador
				if(!(Arrays.asList("{","=",";").contains(nodo.siguiente.dato.getValor()))) 
					impresion.add("Error sinatactico en la linea "+to.getLinea()+" se esparaba un simbolo");
				else
					if(nodo.anterior.dato.getValor().equals("class")) 
					{
						identi.add(new Identificador(to.getValor()," ", "class"));
					}
				break;
		
			case Token.TIPO_DATO:
			case Token.CLASE:
				// si lo anterior fue modificador
				if (nodo.anterior!=null)
					if(cuenta(nodo.siguiente.dato.getValor())>1) {
						
					}else {
						if(nodo.anterior.dato.getTipo()==Token.MODIFICADOR) {
							if(nodo.siguiente.dato.getTipo()!=Token.IDENTIFICADOR) 
								impresion.add("Error sinatactico en la linea "+to.getLinea()+" se esparaba un identificador");
					}else
						impresion.add("Error sinatactico en la linea "+to.getLinea()+" se esperaba un modificador");
					}
					break;
			case Token.SIMBOLO:
				// Verificar que el mismo numero de parentesis y llaves que abren sean lo mismo que los que cierran
				if(to.getValor().equals(";")){
					int aux=0;
					boolean bandera=false;
					//Recorridos de los arboles
					if ((nodo.anterior.anterior.anterior.anterior.dato.getTipo()==Token.CONSTANTE 
							&& nodo.anterior.anterior.anterior.dato.getTipo()==Token.OPERADOR_ARITMETICO && nodo.anterior.anterior.dato.getTipo()==Token.CONSTANTE && nodo.dato.getValor().contains(")"))  ||
							(nodo.anterior.anterior.anterior.anterior.dato.getTipo()==Token.CONSTANTE && nodo.anterior.anterior.anterior.dato.getTipo()==Token.SIMBOLO && nodo.anterior.anterior.dato.getTipo()==Token.OPERADOR_ARITMETICO
							&& nodo.anterior.dato.getTipo()==Token.CONSTANTE) || (nodo.anterior.anterior.anterior.dato.getTipo()==Token.CONSTANTE  && nodo.anterior.anterior.dato.getTipo()==Token.OPERADOR_ARITMETICO
							&& nodo.anterior.dato.getTipo()==Token.CONSTANTE)){

						NodoDoble<Token> nodoaux = nodo;
						NodoDoble<Token> nodoaux2 = nodo;
						NodoDoble<Token> nodoaux3 = nodo;
						while(nodoaux!=null){
							String aux2 = nodoaux.anterior.dato.getValor();
							if(aux2.contains("="))
								break;

							nodoaux = nodoaux.anterior;
						}


						while(nodoaux!=null){
							String aux2 = nodoaux.dato.getValor();
							if(aux2.contains(";"))
								break;

							expresion.add(aux2);
							nodoaux = nodoaux.siguiente;
						}


						ArrayList<String> expresion2 = new ArrayList<String>(expresion);
						int Resultado=0;
						int contador =1;
						
						//Primero revisa si la Expresion tiene parentesis
						for (int i = 0; i < expresion.size(); i++) {
							if(expresion.get(i).contains("(") ){
								if (expresion.get(i).contains("(")){
									int aux5 = i;
									int aux6 = 0 ;
									boolean banderaParentesis = false;

									for (int j = 0; j < expresion.size(); j++) {
										if(expresion.get(j).contains(")")){
											aux6 = j;
											break;
										}
									}

									while(!banderaParentesis){
										for (int j = aux5; j < aux6; j++) {
											if(expresion.get(j).contains("/")){
												Resultado =  dividir(expresion.get(j-1), expresion.get(j+1));
												expresion2.set(j,"temporal"+contador);
												arbol.add(new Arbol("/",expresion2.get(j-1),expresion2.get(j+1),expresion2.get(j)));
												expresion2.remove(j+1);
												expresion2.remove(j-1);

												expresion.set(j,Resultado+"" );
												expresion.remove(j+1);
												expresion.remove(j-1);

												aux6 = aux6 - 2;
												contador++;
													
												
											}
						// Multiplicacion
				if(expresion.get(j).contains("*")) {
					resultado= multiplicar(expresion.get(j-1), expresion.get(j+1));
					expresion2.set(j,"temporal"+contador);
					
					arbol.add(new Arbol("*",expresion2.get(j-1,expresion2.get(j+1),exprecion2.get(j))));
					expresion2.remove(j+1);
					expresion2.remove(j-1);
					aux6 = aux6 -2;
					
					contador++;
				}
						//Suma
				if (expresion.get(1+2).contains("+")) {
					resultado= sumar(expresion.get(i+1),expresion.get(1+3));
					expresion2.set(1+2,"temporal"+contador);
					arbol.add(new arbol("+",expresion2.get(i+1),expresion2.get(1+3),expresion2.get(1+2)));
					expresion2.remove(1+3);
					expresion2.remove(1+1);
					
					expresion.set(i+1,resultado+"");
					exresion.remove(1+2);
					expresion.remove(i+2);
					contador++;
				}
						//Resta
				if(expresion.get(1+2).contains("-")) {
					Resultado = restar(expresion.get(i+1),expresion.get(1+3));
					expresion2.set(i+2,"temporal"+contador);
					arbol.add(new Arbol("-",expresion2.get(i+1),expresion2.get(i+3),expresion2.get(i+2)));
					expresion2.remove(i+3);
					expresion2.remove(i+1);
					
					expresion.set(i+1,resultado+"" );
					expresion.remove(i+2);
					expresion.remove(i+2);
					contador++;
				}
				if(expresion.get(i+2).contains(")")) {
					expresion.remove(i+2);
					expresion.remove(i);
					expresion2.remove(i+2);
					expresion2.remove(i);
					banderaParentesis= true;
				}
				//checa los parentesis qat
				//Division
				for(int i=0;i< expresion.size();i++) {
					if(expresion.get(i).contains("/")){
						Resultado = dividr(expresion.get(i-1),expresion.get(i+1));
						expresion2.set(i,"temporal"+contador);
						arbol.add(new Arbol("/",expresion2.get(i-1),expresion2.get(i+1),expresion2.get(i)));
						expresion2.remove(i+1);
						expresion2.remove(i-1);
						
						expresion.set(i-1,resultado+"");
						expresion.remove(i);
						expresion.remove(i);
						i--;
						contador++;
					}
					else if(expresion.get(i).contains("*")|| expresion.get(i).contains("/")) {
						if(expresion.get(i).contains("*")) {
							resultado = multiplicar(expresion.get(i-1),expresion.get(i+1));
							expresion2.set(i,"temporal"+contador);
							arbol.add(new arbol("*",expresion2.get(i-1),expresion2.get(i+1),expresion2.get(i)));
							
							expresion2.remove(i+1);
							expresion2.remove(i-1);
							expresion.set(i-1,resultado+"");
							expresion.remove(i);
							expresion.remove(i);
							i--;
							contador++;
						}
					}
						
					}
				for (int i=0;i<expresion.size();i++) {
					if(expresion.get(i).contains("+")) {
						Resutado= Sumar(expresion.get(i-1),expresion.get(i+1));
						expresion2.set(i,"temporal"+contador);
						arbol.add(new Arbol("+",expresion2.get(i-1),expresion2.get(i+1),expresion2.get(i)));
						
						expresion2.remove(i+1);
						expresion2.reomove(i-1);
						expresion.set(i-1);
						expresion.set(i-1,Resultado+"");
						expresion.remove(i);
						expersion.remove(i);
						i--;
						contador++;
					}
					else if(expresion.get(i).contains("-")) {
						if(expresion.get(i).contains("-")) {
							Resultado= restar(expresion.get(i-1),expresion.get(i+1));
							expresion2.set(i,"temporal"+ocntador);
							arbol.add(new Arbol("-",expresion2.get(i-1),expresion2.get(i+1),expresion2.get(i)));
							
							expresion2.remove(i+1);
							expresion2.reomove(i-1);
							
							expresion.set(i-1,Resultado+"");
							expresion.remove(i);
							expresion.remove(i);
							i--;
							contador++;
						}
					}
				}
				}
						//Hacer Variables y otras cosas :b-- cuate antrax
					    
						//Continuan los signos
					if(cuenta("{")!=cuenta("}"))
						impresion.add("Error sinatactico en la linea "+to.getLinea()+ " falta un {");
				}else if(to.getValor().equals("{")) {
					if(cuenta("{")!=cuenta("}"))
						impresion.add("Error sinatactico en la linea "+to.getLinea()+ " falta un }");
				}
				else if(to.getValor().equals("(")) {
					if(cuenta("(")!=cuenta(")"))
						impresion.add("Error sinatactico en la linea "+to.getLinea()+ " falta un )");
					else
					{
						if(!(nodo.anterior.dato.getValor().equals("if")&&nodo.siguiente.dato.getTipo()==Token.CONSTANTE)) {
							impresion.add("Error sinatactico en la linea "+to.getLinea()+ " se esperaba un valor");
						}
					}
				}else if(to.getValor().equals(")")) {
					if(cuenta("(")!=cuenta(")"))
						impresion.add("Error sinatactico en la linea "+to.getLinea()+ " falta un (");
				}
				// verificar la asignacion
				else if(to.getValor().equals("=")){
					if(nodo.anterior.dato.getTipo()==Token.IDENTIFICADOR) {
						if(nodo.siguiente.dato.getTipo()!=Token.CONSTANTE)
							impresion.add("Error sinatactico en la linea "+to.getLinea()+ " se esperaba una constante");
						else {
							if(nodo.anterior.anterior.dato.getTipo()==Token.TIPO_DATO)
								
							//Por si se llega a repetir
							if(cuenta(nodo.anterior.dato.getValor())>=2) {
								
							
							}
					}else 
				impresion.add("Error sinatactico en linea "+to.getLinea()+ " se esperaba un identificador");
			
				break;

			case Token.CONSTANTE:
				if(nodo.anterior.dato.getValor().equals("="))
					if(nodo.siguiente.dato.getTipo()!=Token.OPERADOR_ARITMETICO&&nodo.siguiente.dato.getTipo()!=Token.CONSTANTE&&!nodo.siguiente.dato.getValor().equals(";"))
						impresion.add("Error sinatactico en linea "+to.getLinea()+ " asignacion no valida");
				break;
			case Token.PALABRA_RESERVADA:
				// verificar estructura de if
				if(to.getValor().equals("if"))
				{
					if(!nodo.siguiente.dato.getValor().equals("(")) {
						impresion.add("Error sintactico en linea "+to.getLinea()+ " se esperaba un (");
					}
				}
				else 
				{
					// si es un else, buscar en los anteriores y si no hay un if ocurrira un error
					NodoDoble<Token> aux = nodo.anterior;
					boolean bandera=false;
					while(aux!=null&&!bandera) {
						if(aux.dato.getValor().equals("if"))
							bandera=true;
						aux =aux.anterior;
					}
					if(!bandera)
						impresion.add("Error sinatactico en linea "+to.getLinea()+ " else no valido");
				}
				break;
			case Token.OPERADOR_LOGICO:
				// verificar que sea  'numero' + 'operador' + 'numero' 
				if(nodo.anterior.dato.getTipo()!=Token.CONSTANTE) 
					impresion.add("Error semantico en linea "+to.getLinea()+ " se esperaba una constante");
				if(nodo.siguiente.dato.getTipo()!=Token.CONSTANTE)
					impresion.add("Error semantico en linea "+to.getLinea()+ " se esperaba una constante");
				break;
				
			case Token.OPERADOR_ARITMETICO:
				if(nodo.anterior.dato.getTipo()!=Token.CONSTANTE) 
					impresion.add("Error semantico en linea "+to.getLinea()+ " se esperaba una constante");
				if(nodo.siguiente.dato.getTipo()!=Token.CONSTANTE)
					impresion.add("Error semantico en linea "+to.getLinea()+ " se esperaba una constante");

				String aux1="", aux2="";
				aux1 = TipoDato(nodo.anterior.dato.getValor());
				aux2 = TipoDato(nodo.siguiente.dato.getValor());
				if(!aux1.equals(aux2))
					impresion.add("No se puede pude realizar la operacion en la linea "+to.getLinea());
				break;
			}
			analisisSintactico(nodo.siguiente); // buscar el siguiente de forma recursiva

			return to;
		}
		return  vacio;// para no regresar null y evitar null pointer
	}
	//Analisis Lexico :b
	public void analisisLexico(String token) {
		int tipo=0;
		//Se usan listas con los tipos de token
		
		if(Arrays.asList("public","static","private").contains(token)) 
			tipo = Token.MODIFICADOR;
		else if(Arrays.asList("if","else").contains(token)) 
			tipo = Token.PALABRA_RESERVADA;
		else if(Arrays.asList("int","char","float","boolean").contains(token))
			tipo = Token.TIPO_DATO;
		else if(Arrays.asList("(",")","{","}","=",";").contains(token))
			tipo = Token.SIMBOLO;
		else if(Arrays.asList("<","<=",">",">=","==","!=").contains(token))
			tipo = Token.OPERADOR_LOGICO;
		else if(Arrays.asList("+","-","*","/").contains(token))
			tipo = Token.OPERADOR_ARITMETICO;
		else if(Arrays.asList("true","false").contains(token)||Pattern.matches("^\\d+$",token)||Pattern.matches("[0-9]+.[0-9]+",token)||Pattern.matches("-[0-9]+$",token)  )
			tipo = Token.CONSTANTE;
		else if(token.equals("class")) 
			tipo =Token.CLASE;
		else {
			//Cadenas validas
			Pattern pat = Pattern.compile("^[a-zA-Z]+$");
			Matcher mat = pat.matcher(token);
			if(mat.find()) 
				tipo = Token.IDENTIFICADOR;
			else {
				impresion.add("Error en la linea "+renglon+" token "+token);
				bandera = false;
				return;
			}
		}
		tokens.insertar(new Token(token,tipo,renglon));
		impresion.add(new Token(token,tipo,renglon).toString());
	}
	//Analisis Semantico
	public Token analisisSemantico(NodoDoble<Token> nodo) {
		Token to;
		String cadena=null;
		//por si las moscas 
		if(nodo!=null) {
			to = nodo.dato;
			for(int i=0;i<identi.size();i++) {
				int cont=0;
				boolean repetido = false;
				String bandera= "";
				bandera=identi.get(i).getNombre();
				for(int j=0;j<aux.size();j++) {
					//Para saber si un identificador esta repetido
					if(aux.get(j).equals(identi.get(i).getNombre())) {
						repetido=true;
					}
				}
				for(int k=0; k<identi.size();k++) {
					if(identi.get(k).getNombre().equals(bandera) && !repetido) {
						cont++;
						if(cont>1) {
							impresion.add("Variable repetida en la linea: "+identi.get(k).getPosicion());
							aux.add(identi.get(k).getNombre());
						}
					}
				}
				repetido = false;
			
			//Metodos para validar la asigancion de datos
			if(identi.get(i).getTipo().contains("int")) {
				cadena=identi.get(i).getValor();
				if(!isNumeric(cadena)) {
					impresion.add("Error semantico,se esperaba valor flotante en la linea:"+identi.get(i).getPosicion());
				}
			}
			if(identi.get(i).getTipo().contains("float")) {
				cadena=identi.get(i).getValor();
				if(!isNumeric(cadena)) {
					impresion.add("Error semantico,se esperaba valor flotante en la linea:"+identi.get(i).getPosicion());
				}
			}
			if(identi.get(i).getTipo().contains("double")) {
				cadena=identi.get(i).getValor();
				if(!isNumeric(cadena)) {
					impresion.add("Error semantico,se esperaba valor flotante en la linea:"+identi.get(i).getPosicion());
					
				}
			}
			if(identi.get(i).getTipo().contains("boolean")) {
				cadena=identi.get(i).getValor();
				if(!isNumeric(cadena)) {
					impresion.add("Error semantico,se esperaba valor flotante en la linea:"+identi.get(i).getPosicion());
					
				}
			}
			
		}
		return to;
	}
	return vacio;
	}
	//Hacer Metodos para realizar las operaciones del arbol
//Metodos para validar los valores por medio de una cadena
public static boolean isNumeric(String cadena) {
	try {
		Integer.parseInt(cadena);
		return true;
	}catch(NumberFormatException nfe) {
		return false;
	}
}
public static boolean isFloat(String cadena) {
	try {
		Float.parseFloat(cadena);
		return true;
	}catch(NumberFormatException nfe) 
	{
		return false;
		
	}
}
	public static boolean isDouble(String cadena) {
		try {
			Double.parseDouble(cadena);
			return true;
		}catch(NumberFormatException nfe) {
			return false;
		}
	}
	public static boolean isBoolean(String cadena) {
		if(cadena.equals("true")||cadena.equals("false")){
			return true;
		}
		return false;
	}
		
		////Por si alguien escribe todo pegado

		
	public String separaDelimitadores(String linea){
		for (String string : Arrays.asList("(",")","{","}","=",";")) {
			if(string.equals("=")) {
				if(linea.indexOf(">=")>=0) {
					linea = linea.replace(">=", " >= ");
					break;
				}
				if(linea.indexOf("<=")>=0) {
					linea = linea.replace("<=", " <= ");
					break;
				}
				if(linea.indexOf("==")>=0)
				{
					linea = linea.replace("==", " == ");
					break;
				}
			}
			if(linea.contains(string)) 
				linea = linea.replace(string, " "+string+" ");
		}
		return linea;
	}
	public int cuenta (String token) {

		int conta=0;
		NodoDoble<Token> Aux=tokens.getInicio();
		while(Aux !=null){
			if(Aux.dato.getValor().equals(token))
				conta++;
			Aux=Aux.siguiente;
		}	
		return conta;
	}
	//metodo para saber el tipo de dato
	public String TipoDato(String aux) {
		if(Pattern.matches("[0-9]+",aux))
			return "int";
		if(Pattern.matches("[0-9+.[0-9]",aux))
			return "float";
		if(Pattern.matches("true+",aux))
			return "boolean";
		return"";
	}
	public ArrayList<String> getmistokens() {
		return impresion;
	}
	
	
}
