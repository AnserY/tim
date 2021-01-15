## Qu'est ce qu'une Ontologie ?

* https://www.editions-eni.fr/_Download/0b916947-4095-4842-adad-3898c340050f/web-semantique-et-mo-extrait-du-livre.pdf
* https://tel.archives-ouvertes.fr/tel-02103695/document

## Quelle librairie utiliser : OWL API Vs Jena ##

Bien que les deux soient très bien documentées et qu'elles aient les mêmes fonctionnalités, je favoriserai plutôt de développer avec OWL API. La documentation de Jena est très longue et cela pour seulement quelques lignes de code. En effet, sa documentation traite à la fois de l'implémentation en Java et de la "théorie", ce qui peut rapidement ralentir la compréhension de la construction du code. Pour OWL API, j'ai pu trouver rapidement des exemples complexes quant à l'utilisation des ontologies en lignes de code.

La documentation de Jena est très intéressante notamment si nous avons des lacunes sur certaines connaissances théoriques. Enfin si elles possèdent le même type de fonction, je trouve celle de OWL API plus compréhensible (moins de paramètres à spécifier dans certaines fonctions de OWL API).

Les différents exemples ci-dessous ont été réalisés avec OWL API.

## Premiers pas

* tutoriel "utiliser Maven dans Eclipse" : http://blog.paumard.org/tutoriaux/eclipse-maven/
* tutoriel "owl Api : a starter's starter : https://github.com/owlcs/owlapi/wiki/Tutorial:-A-starter%27s-starter
* introduction à OWL API : http://syllabus.cs.manchester.ac.uk/pgt/2017/COMP62342/introduction-owl-api-msc.pdf

## Qu'est ce qu'un Raisonneur ?

Il est nécessaire de pouvoir raisonner sur des ontologies pour avoir de nouvelles connaissances. Les raisonneurs permettent de contrôler la cohérence des ontologies, de vérifier si certaines classes sont insatisfaisables et de pouvoir gérer les différentes hiérarchies des classes et des relations. Un raisonneur permet d’inférer de nouvelles connaissances.

Il existe différents raisonneurs que l'on peut utilisé en Java : 

* FaCT++ : `uk.ac.manchester.cs.factplusplus.owlapiv3.FaCTPlusPlusReasonerFactory`
* HermiT : `org.semanticweb.HermiT.ReasonerFactory`
* Pellet : `com.clarkparsia.pellet.owlapiv3.PelletReasonerFactory`
* JFact : `uk.ac.manchester.cs.jfact.JFactFactory`

Un exemple de configuration pour HermiT : https://github.com/phillord/hermit-reasoner/blob/master/examples/org/semanticweb/HermiT/examples/HermiTConfigurations.java .

Un exemple de code Java : 

    OWLOntologyManager m=OWLManager.createOWLOntologyManager();
    OWLOntology o=m.loadOntologyFromOntologyDocument(IRI.create("someIRI"));
    Reasoner hermit=new Reasoner(o);
    System.out.println(hermit.isConsistent()); // Teste la cohérence de l'ontologie "o"

## OWL API ##

L'API OWL est une API Java et une implémentation de référence pour créer, manipuler et sérialiser des ontologies OWL. De nombreux projets utilisent OWL-API comme outil de développement. A titre d’exemple, on peut citer Protégé-4.

Dépendances pour le fichier pom.xml :

    <dependency>
        <groupId>net.sourceforge.owlapi</groupId>
        <artifactId>owlapi-distribution</artifactId>
        <version>5.1.0</version>
    </dependency> 

On présente trois exemples d'implémentation qui nous permettent d'effectuer des actions intéressantes : 

* charger une ontologie
* sauvegarder une nouvelle ontologie
* création d'une restriction
* fusion de deux ontologies
* effectuer une requête avec l'utilisation d'un raisonneur

Dans ces exemples, le code est commenté pour décrire les actions effectuées. Ce code s'inspire de celui disponible ici : https://github.com/owlcs/owlapi/blob/version4/contract/src/test/java/org/semanticweb/owlapi/examples/Examples.java . 

Import : 

    import java.util.Set;
    import org.semanticweb.owlapi.apibinding.OWLManager;
    import org.semanticweb.owlapi.model.AddAxiom;
    import org.semanticweb.owlapi.model.IRI;
    import org.semanticweb.owlapi.model.OWLAxiom;
    import org.semanticweb.owlapi.model.OWLClass;
    import org.semanticweb.owlapi.model.OWLClassExpression;
    import org.semanticweb.owlapi.model.OWLDataFactory;
    import org.semanticweb.owlapi.model.OWLObjectProperty;
    import org.semanticweb.owlapi.model.OWLObjectPropertyExpression;
    import org.semanticweb.owlapi.model.OWLOntology;
    import org.semanticweb.owlapi.model.OWLOntologyCreationException;
    import org.semanticweb.owlapi.model.OWLOntologyManager;
    import org.semanticweb.owlapi.model.OWLOntologyStorageException;
    import org.semanticweb.owlapi.model.OWLSubClassOfAxiom;
    import org.semanticweb.owlapi.reasoner.OWLReasoner;
    import org.semanticweb.owlapi.reasoner.OWLReasonerFactory;
    import org.semanticweb.owlapi.reasoner.structural.StructuralReasonerFactory;
    import org.semanticweb.owlapi.util.OWLOntologyMerger;

Création d'une restriction : 

    	// ** Création de notre objet sur lequel nous voulons travailler **
        OWLOntologyManager man = OWLManager.createOWLOntologyManager();
        String base = "http://org.semanticweb.restrictionexample";
        OWLOntology ont = man.createOntology(IRI.create(base));
        OWLDataFactory factory = man.getOWLDataFactory();
        // *****************************************************************
        //Ajout d'un axiome pour indiquer que toutes les têtes ont au moins un nez.
        // Création d'une restriction pour décrire (hasPart some Nose)
        OWLObjectProperty hasPart = factory.getOWLObjectProperty(IRI.create(base + "#hasPart"));
        OWLClass nose = factory.getOWLClass(IRI.create(base + "#Nose"));
        OWLClassExpression hasPartSomeNose = factory.getOWLObjectSomeValuesFrom(hasPart, nose);
        OWLClass head = factory.getOWLClass(IRI.create(base + "#Head"));
        OWLSubClassOfAxiom ax = factory.getOWLSubClassOfAxiom(head, hasPartSomeNose);
        // Add the axiom to our ontology
        AddAxiom addAx = new AddAxiom(ont, ax);
        man.applyChange(addAx);

Fusionner deux ontologies : 

    public static void shouldMergeOntologies() throws OWLOntologyCreationException,OWLOntologyStorageException {
		// Ici, on load deux ontologies existantes et on sauvegarde le nouveau fichier en local
		OWLOntologyManager man = OWLManager.createOWLOntologyManager();
		man.loadOntologyFromOntologyDocument(IRI.create("http://www.w3.org/People/Berners-Lee/card")); // Ici je prends deux ontologies valables (URL existante), ce qui est assez rare lors de mes recherches
		man.loadOntologyFromOntologyDocument(IRI.create("http://www.w3.org/People/Berners-Lee/card"));
		// On créait une ontologie merger
		OWLOntologyMerger merger = new OWLOntologyMerger(man);
		IRI mergedOntologyIRI = IRI.create("./merge.owl");
		OWLOntology merged = merger.createMergedOntology(man, mergedOntologyIRI);
		for (OWLAxiom ax : merged.getAxioms()) { // deprecated method
		    System.out.println(ax);
		}
		// Save to RDF/XML
		man.saveOntology(merged, new RDFXMLOntologyFormat(), // deprecated method
		        IRI.create("file:/C:/Users/myowl/monexemple.owl")); // mettre une IRI absolue
    }

Requête avec raisonneur : 

        // On utilise l'ontologie de la pizza PIZZA_IRI = "https://protege.stanford.edu/ontologies/pizza/pizza.owl";
        IRI documentIRI = IRI.create(PIZZA_IRI);
        OWLOntologyManager man = OWLManager.createOWLOntologyManager();
        OWLOntology ont = man.loadOntologyFromOntologyDocument(documentIRI);
        String prefix = ont.getOntologyID().getOntologyIRI() + "#";
        OWLReasonerFactory reasonerFactory = new StructuralReasonerFactory();
        OWLReasoner reasoner = reasonerFactory.createNonBufferingReasoner(ont);
        OWLClass margheritaPizza = man.getOWLDataFactory().getOWLClass(
                IRI.create(prefix + "Margherita"));

        // avec JalapenoPepperTopping
        OWLClass vegTopping = man.getOWLDataFactory().getOWLClass(
                IRI.create(prefix + "JalapenoPepperTopping"));
        // On peut aussi se demander si les instances d'une classe doivent avoir une propriété
        OWLClass mozzarellaTopping = man.getOWLDataFactory().getOWLClass(
                IRI.create(prefix + "MozzarellaTopping"));
        OWLObjectProperty hasOrigin = man
                .getOWLDataFactory()
                .getOWLObjectProperty(IRI.create(prefix + "hasCountryOfOrigin"));
        if (hasProperty(man, reasoner, mozzarellaTopping, hasOrigin)) { 
            System.out.println("Instances of " + mozzarellaTopping
                    + " have a country of origin");
        }


### SPARQL avec OWL API ###

sparql-dl-api est une api SPARQL compatible avec OWL API.

Dépendances pour le fichier pom.xml : 

	<dependency>
		<groupId>edu.stanford.protege</groupId>
		<artifactId>de-derivo-sparqldlapi</artifactId>
		<version>2.0.0</version>
	</dependency>

	<dependency>
		<groupId>net.sourceforge.owlapi</groupId>
		<artifactId>owlapi-distribution</artifactId>
		<version>5.1.0</version>
	</dependency>



Code Java venant de https://github.com/protegeproject/sparql-dl-api/blob/master/src/main/java/de/derivo/sparqldlapi/examples/Example_Extended.java : 

		try {

			OWLOntologyManager manager = OWLManager.createOWLOntologyManager();
            OWLOntology ont = manager.loadOntologyFromOntologyDocument(IRI.create("http://www.w3.org/People/Berners-Lee/card"));
            StructuralReasonerFactory factory = new StructuralReasonerFactory();
			OWLReasoner reasoner = factory.createReasoner(ont);
			

			engine = QueryEngine.create(manager, reasoner);

            processQuery(
				"SELECT * WHERE {}"
			);

            processQuery(
            		"PREFIX foaf:  <http://xmlns.com/foaf/0.1/>\r\n" + 
        					"SELECT ?name\r\n" + 
        					"WHERE {\r\n" + 
        					"    ?person foaf:name ?name .\r\n" + 
        					"}"
			);

        }
        catch(UnsupportedOperationException exception) {
            System.out.println("Unsupported reasoner operation.");
        }
        catch(OWLOntologyCreationException e) {
            System.out.println("Could not load the ontology: " + e.getMessage());
        }

Ici, processQuery est une fonction qui execute les requêtes puis affiche les résultats de manière lisible.


    public static void processQuery(String q)
	{
		try {
			long startTime = System.currentTimeMillis();
			
			// Create a SPARQL-DL query
			Query query = Query.create(q);
			
			System.out.println("Excecute query:");
			System.out.println(q);
			System.out.println("-------------------------------------------------");
			
			// Execute the query and generate the result set
			QueryResult result = engine.execute(query);
			if(query.isAsk()) {
				System.out.print("Result: ");
				if(result.ask()) {
					System.out.println("yes");
				}
				else {
					System.out.println("no");
				}
			}
			else {
				if(!result.ask()) {
					System.out.println("Query has no solution.\n");
				}
				else {
					System.out.println("Results:");
					System.out.print(result);
					System.out.println("-------------------------------------------------");
					System.out.println("Size of result set: " + result.size());
				}
			}

			System.out.println("-------------------------------------------------");
			System.out.println("Finished in " + (System.currentTimeMillis() - startTime) / 1000.0 + "s\n");
		}
        catch(QueryParserException e) {
        	System.out.println("Query parser error: " + e);
        }
        catch(QueryEngineException e) {
        	System.out.println("Query engine error: " + e);
        }
	}
}

## SPARQL avec JENA ##

SPARQL est un langage de requête et un protocole qui permet de rechercher, d'ajouter, de modifier ou de supprimer des données RDF disponibles à travers Internet.  Il s’appuie sur des triplets que constituent les graphes RDF : sujet,  prédicat, objet.

Exemple de triplets : ( fruit *aCouleur* jaune ) → Se lit : Fruit possédant la couleur jaune (*aCouleur* est un prédicat).

SPARQL effectue des requêtes sur des graphes. Il inclut donc les IRI, un sous-ensemble de références URI RDF qui omet les espaces. Ces IRI sont absolus en SPARQL.

Exemple d’un fichier RDF : 

    <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns# [archive]"
      xmlns:foaf="http://xmlns.com/foaf/0.1/ [archive]"
      xmlns:rss="http://purl.org/rss/1.0/ [archive]"
      xmlns:dc="http://purl.org/dc/elements/1.1/ [archive]">
	    <foaf:Person rdf:about="http://example.net/Paul_Dupont [archive]">
		    <foaf:name>Paul Dupont</foaf:name>
		    <foaf:img rdf:resource="http://example.net/Paul_Dupont.jpg [archive]"/>
		    <foaf:knows rdf:resource="http://example.net/Pierre_Dumoulin [archive]"/>
	    </foaf:Person>
	    <foaf:Person rdf:about="http://example.net/Pierre_Dumoulin [archive]">
		    <foaf:name>Pierre Dumoulin</foaf:name>
		    <foaf:img rdf:resource="http://example.net/Pierre_Dumoulin.jpg [archive]"/>
	    </foaf:Person>
	    <foaf:Image rdf:about="http://example.net/Paul_Dupont.jpg [archive]">
		    <dc:description>Photo d'identité de Paul Dupont</dc:description>
	    </foaf:Image>
	    <foaf:Image rdf:about="http://example.net/Pierre_Dumoulin.jpg [archive]">
		    <dc:description>Photo d'identité de Pierre Dumoulin</dc:description>
	    </foaf:Image>
    </rdf:RDF>

Exemple de requête SPARQL:

     PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
     PREFIX foaf: <http://xmlns.com/foaf/0.1/>
     PREFIX dc: <http://purl.org/dc/elements/1.1/>
     SELECT DISTINCT ?nom ?image ?description
     WHERE {
 	    ?personne rdf:type foaf:Person.
 	    ?personne foaf:name ?nom.
 	    ?image rdf:type foaf:Image.
 	    ?personne foaf:img ?image.
 	    ?image dc:description ?description
     }

Le nom des variables est précédé de « ? ». SELECT permet de sélectionner l’ensemble des tuples. « WHERE » correspond aux contraintes.

La première contrainte se lit : « si la personne est de type *Person* » au sens de l’ontologie FoaF.

Note : Une requête peut se faire sans PREFIX mais il est préférable de les utiliser pous plus de compréhension.

SPARQL possède également des requêtes semblables à SQL : ORDER BY, LIMIT, FILTER ...

Source :

* http://www-igm.univ-mlv.fr/~dr/XPOSE2009/Le%20Web%203.0/technologies.html
* https://www.w3.org/TR/sparql11-query/
* https://www.wikidata.org/wiki/Wikidata:SPARQL_tutorial/fr

### En Java ###

Ici on va effectuer une simple requête sur une ontologie existante disponible à cette adresse : http://www.w3.org/People/Berners-Lee/card . On utilise ici Apache Jena : https://jena.apache.org/index.html et notamment ARQ (A SPARQL processor for Jena) pour effectuer des requêtes SPARQL en Java.

Dépendances pour le fichier pom.xml : 

    <dependency>
        <groupId>org.aksw.jena-sparql-api</groupId>
        <artifactId>jena-sparql-api-core</artifactId>
        <version>3.7.0-3</version>
    </dependency>

    <dependency>
        <groupId>org.apache.jena</groupId>
        <artifactId>jena-core</artifactId>
        <version>3.12.0</version>
    </dependency>

Code Java : 

		FileManager.get().addLocatorClassLoader(Main.class.getClassLoader());
		Model model = FileManager.get().loadModel("http://www.w3.org/People/Berners-Lee/card");
		String myQuery = "PREFIX foaf:  <http://xmlns.com/foaf/0.1/>\r\n" + 
				"SELECT ?name\r\n" + 
				"WHERE {\r\n" + 
				"    ?person foaf:name ?name .\r\n" + 
				"}";
		Query query = QueryFactory.create(myQuery);
		QueryExecution qexec = QueryExecutionFactory.create(query,model);
		try {
			ResultSet results = qexec.execSelect();
			while(results.hasNext()) {
				System.out.println(results.getResourceModel()+"\n");
				QuerySolution qs = results.next();
			}
		} finally {
			qexec.close();
		}

Source :

* https://jena.apache.org/index.html
* https://jena.apache.org/documentation/query/index.html
* SPARQL-Generate : https://ci.mines-stetienne.fr/sparql-generate/get-started.html
* ARQ : https://jena.apache.org/documentation/query/


## SWRL

Une règle : Si une Personne *x* et enfant de *y* et que *y* possède un frère *z* alors *x* a pour oncle *z*.

Exemple SWRL : `:hasParent(?x,?y), :hasBrother(?y,?z) -> :hasUncle(?x,?z)`

Des moteurs d’inférences supportent SWRL : Bossam, Hoolet, KAON2, Pellet, RacerPro, R2ML et Sesame.

### Implémentation en Java

Il y a différents problèmes lors de la mise en place de SWRL en Java **sous Eclipse**.

* 1re tentative : j'ai ajouté les dépendances de SWRL API dans le fichier pom.xml. Cela engendre un problème de configuration de build path, en cause : une fonction présente dans SWRL API et dans JRE ( import java.*; ). Ce problème est spécifique à Eclipse. 
* 2nd tentative : J'ai donc généré un fichier .jar contenant les fonctions de SWRL. Pas de problème entre les librairies. Le problème survient quand je lance un premier programme basique créant juste un objet "SWRLRuleEngine". Le problème est que je dois intégrer une implémentation du moteur de règles basé sur SWRL API, qui n'est pas présent dans le fichier .jar. Quand j'intègre ce moteur, Eclipse me renvoie une erreur connue par beaucoup de développeurs sous cette IDE qui est : *The type java.lang.String cannot be resolved. It is indirectly referenced fromrequired .class files*. Bien que beaucoup de documentation sur ce sujet ( voir : https://stackoverflow.com/questions/18075343/java-project-in-eclipse-the-type-java-lang-object-cannot-be-resolved-it-is-ind) et ayant testé toutes les possibilités et chercher d'autres moyens, Eclipse me renvoie toujours cette même erreur.

* 3ème tentative : Dans le lien que j'ai indiqué entre parenthèses, la dernière solution était d'utiliser L'IDE NetBeans, qui gérait mieux ce type de problématique récurrente sous Eclipse. J'ai donc repris mes deux projets : celui de la première tentative et celui de la seconde tentative. Le résultat est que NetBeans n'a pas renvoyé d'erreur et a exécuté le code Java.

Conclusion: Sous NetBeans les deux codes que j'ai sont corrects. J'ai essayé beaucoup de choses sous Eclipse :

* Librairies différentes
* Versions différentes
* Revoir le code
* Supprimer le projet du workspace et le remettre
* Créer un autre workspace
* Ajouter des fichiers .jar plutôt que Maven
* Modifier le build path
* Supprimer le JRE et le remettre
* Fermer Eclipse et relancer
* Tester plusieurs pom.xml
* Clean le projet Java

### Implémentation Java

pom.xml :

    <dependency>
        <groupId>edu.stanford.swrl</groupId>
        <artifactId>swrlapi</artifactId>
        <version>2.0.7</version>
    </dependency>

    <dependency>
        <groupId>edu.stanford.swrl</groupId>
        <artifactId>swrlapi-drools-engine</artifactId>
        <version>1.1.1</version>
    </dependency>


Premier exemple de code Java :

            OWLOntologyManager ontologyManager = OWLManager.createOWLOntologyManager();
            OWLOntology ontology = ontologyManager.loadOntologyFromOntologyDocument(new File("C:\\Users\\Utilisateur\\myowl\\file_owl.owl"));
            SQWRLQueryEngine queryEngine = SWRLAPIFactory.createSQWRLQueryEngine(ontology);
            SQWRLResult result = queryEngine.runSQWRLQuery("Q1", "Person(?p) -> sqwrl:select(?p)");
            while (result.next()) {
                System.out.println("Person : " + result.getValue("p"));
            }


Ici, on charge une ontologie existante (chemin spécifique à Windows). Ensuite, on crée notre requête SWRL nommée "Q1" et on affiche le résultat. Voici un lien sur SQWRLQueryAPI : https://github.com/protegeproject/swrlapi/wiki/SQWRLQueryAPI#processing-result-columns .

Le premier exemple nous renvoie le résultat d'une requête, qui peut être aussi effectué avec SPARQL. Ici, on montre simplement une implémentation utilisant les méthodes vitales pour pouvoir effectuer des règles SWRL et les appliquer.

Un exemple d'utilisation très intéressant de SQWRL est de pouvoir remplir une nouvelle classe en énonçant une règle. Voici un exemple : nous avons des personnes possédant un nom et un âge. On veut définir une nouvelle classe permettant de dire si une personne est un adulte. Pour la remplir, on créait la classe "Adult" comme nous l'avons déjà vu avec OWL API : `OWLClass adult = factory.getOWLClass(IRI.create(ontologyIRI + "#Adult"));` . Un adulte étant une personne possédant un âge supérieur à 17 ans, nous pouvons remplir directement la classe grâce à SWRL et à une de ses méthodes ":greaterThan" : `SWRLAPIRule rule = ruleEngine.createSWRLRule("IsAdult-Rule", 
   "Person(?p) ^ hasAge(?p, ?age) ^ swrlb:greaterThan(?age, 17) -> Adult(?p)");`.




Autres sources pour SWRL :

* https://hal.archives-ouvertes.fr/hal-00709139/file/Fortineau_PLM12_SWRL_as_a_rule_language_for_ontology_based_models_in_power_plant_design.pdf
* https://protege.stanford.edu/conference/2007/slides/08.01_OConnor.pdf
* https://pdfs.semanticscholar.org/af72/094efcd3586000fb6a8c1e8401a31ef3e13a.pdf
* Code source de la méthode SQWRLResult : https://github.com/protegeproject/swrlapi/blob/master/src/main/java/org/swrlapi/sqwrl/SQWRLResult.java
* Fil de discussion sur des erreurs d'implémentation : https://github.com/protegeproject/swrlapi/issues/14

## RDF (Resource Description Framework)

Resource Description Framework (RDF) est un modèle de graphe, développé par le W3C, servant à décrire de façon formelle les ressources Web et leurs métadonnées afin de permettre le traitement automatique de telles descriptions. C'est le langage de base du Web sémantique. L'une des syntaxes (ou sérialisations) de ce langage est RDF/XML.

https://www.w3.org/TR/2004/NOTE-owl-parsing-20040121/



## IRI (Internationalized Resource Identifier)

Sur Internet, IRI ou Internationalized Resource Identifier est un type d'adresse informatique prenant en compte les divers alphabets utilisés dans les différentes langues du monde. 

Pour les ontologies : L'adresse suivante désigne une ontologie : <http://www.co-ode.org/ontologies/pizza/pizza.owl>.

Dans OWL API, on reprend cette adresse pour la charger. Maintenant, une pizza peut être nappée avec de la mozzarella. Ainsi nous aurons "MozzarellaTopping" comme sous-classe. Cela se traduit par <http://www.co-ode.org/ontologies/pizza/pizza.owl#MozzarellaTopping> . 

https://www.w3.org/International/O-URL-and-ident.html

## Turtle

Turtle (Terse RDF Triple Language) est une syntaxe d'un langage qui permet une sérialisation non-XML des modèles RDF. 

https://www.w3.org/TR/turtle/
