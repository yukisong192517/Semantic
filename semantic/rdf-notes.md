# RDF AND OWL

## owl

### OWL DL

- description logic based, SHOIN, Complete and decidable, higher worst case complexity than OWL lite

- support all owl constructs， with some restrictions
取数据类型值的属性不能被标记为反向功能
类，属性，个人和数据类型值是不相交的

### ontology header
~~~
<owl: Ontology rdf:about="">
 
<!--- 主语通过rdf: description 中的rdf:about 定义 -->

<owl:versionInfo>1.4</owl:versionInfo>
<rdfs:comment>An example ontology</rdfs:comment>  <owl:imports     rdf:resource="http://www.example.org/base"/></owl:Ontology> 
~~~

#### version support

- owl: versionInfo（version number）

- owl: priorVersion (indicates previous version)

- owl: backwardCompatibleWith
specified ontology is a previous version of this one, cpmpatible with it 

- owl: incompatible with previous version of this one , is incimpatible with it.

- owl: DeprecatedClass & owl: DeprecatedProperty: classes and properties may be marked as deprecated

### OWL Class types

- owl: Class

	distinct from rdfs:class - needed for OWL Lite/DL

- owl: Thing
	- includes everything 
- owl: Nothing
	- empty class

### OWL Property types

- owl: ObjectProperty
	- resource-valued properties
	
```
:rents rdf:type   owl:ObjectProperty;
	rdf: domain 	:Person ;
	rdfs: range		:Apartment ;
	rdfs: subPropertyOf  :livesIn ;
```

- owl: DatatypeProperty
	- The class of literal- valued Properties

```
:age rdfs:type  owl: DatatypeProperty ;
	rdfs: range   xsd: nonNegativeInteger
``` 
- owl: AnnotationProperty
	- type properties which annotate classes and properties 标注属性不带任何owl2 dl直接予以含义的属性，会被逻辑推理机忽略，但在rdf模式和owl2 full中考虑， 特别用于本体类属性和个体添加可读的标签，注释和解释
	
	```
	:label		rdf:type		owl: AnnotationProperty
				rdf:range		rdf:PlainLiteral
				rdf:subPropertyof	rdf:label
<!--- :label属性是annotation，值域是plainliteral -->				
	:Apartment	:label	"Apartment"@en
	"Appartement"@nl
<!--- 定义：label是rdfs：label的字属性，并给：Apartment类 英文，荷兰语标签-->
	```

## OWL VS RDF 

semantic of a description logic is specified by interpretation function:instances,classes, properties

reflexive definition of RDF: some resources are treated as both <mark>classes and instances</mark>,or <mark>instances and properties</mark>

## OWL Restriction

class expression: constraints on property

- local cardinality constraints (≤ n R, ≥ n R, = n R) 局部基数限制（基数限制约束了一个类某个属性可以拥有的值得数目。额外制定这些只必须属于的类，就称这个约束是限定的）

	```
	:Studio
	rdf:type 		owl: Class
	rdfs: subClassOf [rdf:type		owl:Restriction;
	owl: onProperty	:hasRoom;
	owl:cardinality	"1"^^xsd: Integer]
	```

  制定单间公寓只能有一个has room属性的值，可以将其更改为限定对基数限制，通过声明这个基数只对：livingroom，：kitchen 和：bedroom成立
  
	```
	:Studio
	rdf:type 		owl: Class
	rdfs: subClassOf [rdf:type		owl:Restriction;
	owl: onProperty	:isPlayedBy ;
	owl: qualifiedCardinality 	"1"^^xsd: Integer
	owl:onClass [owl:unionOf(:livingroom :Kitchen :Bedroom)]]
	```	
	- owl:minCardinality (≥ n R)“property R has at least n values”	- owl:maxCardinality (≤ n R)“property R has at most n values”	- owl:cardinality (= n R)“property R has exactly n values”
	- example
	
	```
	<owl:Class rdf:about=“#SingleMaltWhisky”>	<owl:equivalentClass>
	<owl:Class>	<owl:intersectionOf rdf:parseType="Collection">
	<owl:Class rdf:about=“#Whisky”/>    <owl:Restriction>    <owl:onProperty rdf:resource=“#distilledBy”/>    <owl:cardinality>1</owl:cardinality>    </owl:Restriction>    </owl:intersectionOf>
    <owl:Class>
    </owl:equivalentClass>
    </owl:Class>
	```
主语：SingleMaltWhisky 等同于 主语：whiskey 与 谓语：distilledby ,通过限制cardinality=1 进行相交得出的类。
SingleMaltWhiskey ≡__Whiskey⊓∃_=1__ _distilledBy 

- local range constraints: define a class based on the type of property values, distinct from global range constraint(rdf: range)
	- ∃R.C: owl:someValueFrom
	
		there exists a value for property of type C
		
	- ∀R.C: owl:allValueFrom
		
		property R has only values of type C
	
	- can only be used with named classes and datatypes in <mark>owl lite </mark>
	- Carnivores are things which eat some things which are animals (Carnivore ≡∃eats.Animal) carnivores是食肉动物

		```
		<owl:Class rdfs: about = "#Carnivore" >
		<owl: equivalenrClass>
			<owl: Restriction>
				<owl: onProperty rdf:resource ="#eats"/>
				<owl: someValueFrom rdf:resource ="#Animal"
			</owl: Restriction>
		</owl:equivalentClass>
		</Class>
		
		```
- local value constraints

	- ∃R.{x}: define a class bassed on the existence of a paticular value在已经存在的特定属性基础上定义一个类
	-  owl: hasValue(∃R.{x}):property R has a value which is X
，属性r 有值x

	```
	<owl:Class rdf:about="#GreenThing">
	<owl:EquivalentClass>
		<owl: Restriction>
			<owl: onProperty rdf:resource="#hasColour"/>
			<owl: hasValue	rdf:resource="#Green"/>
		</owl: Restriction>
	</owl: equivalentClass>
	<owl:Class>	
	```
- restriction format

```
<owl:Restriction>  <owl:onProperty rdf:resource=“property”/>  constraint expression</owl:Restriction>
```

- Set Constructors:
	- owl:intersectionOf (C ⊓ D)
	- owl:unionOf (C ⊔ D)<mark>cannot be used in owl lite</mark>	- owl:complementOf (¬ C)<mark>cannot be used in owl lite</mark>
	- example

		```
		<owl:Class rdf:about=“GreenApple”>		<owl:equivalentClass>    		<owl:Class>      			<owl:intersectionOf rdf:parseType=“Collection”>       	 		<owl:Class rdf:about=“Apple”>       	 		<owl:Restriction>          			<owl:onProperty rdf:resource=“hasColor”/>          			<owl:hasValue rdf:resource=“Green”/>       			</owl:Restriction>     			</owl:intersectionOf>		   </owl:Class>  		</owl:equivalentClass>		</owl:Class>		(GreenApple ≡  Apple ⊓ ∃hasColour. { Green })		
		```
		
- equivalence and identity relations
	- used for mapping
		- owl: SameAs
		- owl: equivalentClass(C≡D)
		- owl: equivalentProperty(R≡S)
		
			```
			<owl: Thing rdf:about="#MorningStar">
				<owl:sameAs rdf:resource="#EveningStar"/>
			</owl:Thing>
			```

- non-equivalence relations
	- owl: DifferentFrom
		- specify a limited unique name assumption
		
		```
		<rdf:Restriction rdf:about="#HarryCorbett">
			<owl:differentFrom rdf:resource="#HarryHCorbett"/>
		</rdf:Restriction>
		```
	- owl:AllDifferent and owl:distinctMembers
		- used to specify a group of mutually distinct individuals.
		
		```
				<owl:Class rdf:ID=“Wine"> 				<rdfs:subClassOf>				<owl:Restriction>        	  	<owl:onProperty rdf:resource=“#hasWineSugar”/>          	<owl:cardinality>1</owl:cardinality>      	  		</owl:Restriction>				</rdfs:subClassOf>				</owl:Class>
								<WineSugar rdf:ID="OffDry"> 
				<owl:differentFrom rdf:resource="#Dry"/> 				<owl:differentFrom rdf:resource="#Sweet"/> 
				</WineSugar>
		```
		
		因为上面限定了1个property只能有一个值，所以当限定既干又甜的时候需要说明dry和sweet differentfrom， 否则就证明dry和sweet是同一种property
		
		```
		<owl:AllDifferent>
  			<owl:distinctMembers rdf:parseType="Collection">
    			<vin:WineColor rdf:about="#Red" />
    			<vin:WineColor rdf:about="#White" />
    			<vin:WineColor rdf:about="#Rose" />
  			</owl:distinctMembers>
		</owl:AllDifferent>
		```				<mark>distinctmembers only can be used in Alldifferent</mark>
		
## Necessary Class Definition

- Primitive/ Partial Definition

	“If we know that something is a X,then it must fulfill the conditions...” Defined using <mark>rdfs:subClassOf</mark>
	Example:Person⊑∃hasBirthdate- Sufficient Class Definition

	Describes a subset of the class (⊒)	“If we know that something has this property,then it belongs to this class...”
		Defined using rdfs:subClassOf – in the other direction	Example:Person⊒∃hasNationalInsuranceNumber- Necessary and sufficient class Definition
	- Defined / complete classes (≡) --- (owl:equivalentClass)
	- Property types:Inverse ---
		Defines a property as the inverse of another property	(R ≡ S-)	
		```
		<owl:ObjectProperty rdf:about=“#hasAuthor”> 		 <owl:inverseOf rdf:resource=“#wrote”/>		</owl:ObjectProperty>
		```
	- Property types - Symmetric
	
		Symmetric properties satisfy the axiom P(x,y) iff P(y,x)
		
		```
		<owl:SymmetricProperty rdf:about=“#hasSibling”/>
		<!--- a 有兄弟姐妹 b, b 也有兄弟姐妹 a -->
		```	- Property types - transitive

		Transitive properties satisfy the axiom P(x,y) and P(y,z) implies P(x,z)		
		```
		<owl:TransitiveProperty rdf:about=“#hasAncestor”/>
		```
		
	- Property types - Functional
		Functional properties satisfy the axiom P(x,y) and P(x,z) implies y=z.
		
		```
		<owl:FunctionalProperty rdf:about=“#hasNINumber”/>
		```
		
		(everyone has only one NI number)
		
		(everyone has only one birthdate)
		
		(but: people may have several means of identification)
		
		某些属性，每个个体通过该属性总是最多存在另一个相关个体
	- InverseFunction:
		Inverse functional properties satisfy the axiom P(y,x) and P(z,x) implies y=z		
		```
		<owl:InverseFunctionalProperty rdf:about=“#hasNINumber”/>   
		```
				(people with the same NI number are the same person)
				(each identification points to only one person)
				(but: many people have the same birthdate)	- Disjoint  Classes `owl:disjointWith`		
		members of one class cannot also be members of some specified other class
		
		```
		<owl:Class rdf:about=“#MaleHuman”>  			<rdfs:subClassOf rdf:resource=“#Human”/> 			 <owl:disjointWith rdf:resource=“#FemaleHuman”/>		</owl:Class>		```	- Enumerated classes
		
		Defines a class as a direct enumeration of its membersowl:one of (C ≡ {a,b,c}), Cannot be extended (closed set)

		```
		<owl:Class rdf:about=“#Continents”>  		<owl:oneOf rdf:parseType=“Collection”>   		 <owl:Thing rdf:about=“#Africa”/>    		<owl:Thing rdf:about=“#Antarctica”/>   			 <owl:Thing rdf:about=“#Oceania”/>    			 <owl:Thing rdf:about=“#Europe”/>   			 <owl:Thing rdf:about=“#North-America”/>   			 <owl:Thing rdf:about=“#South-America”/>   			 <owl:Thing rdf:about=“#Asia”/>  			</owl:oneOf>		</owl:Class>
		```# OWL2

## SYNTACTIC SUGAR: Disjoint Union

Allows us to define a class as the union of a number of other classes, all of which are pairwise disjoint 互不相交```
<rdf:Description>        <rdf:type rdf:resource="http://www.w3.org/2002/07/owl#AllDisjointClasses"/>        	<owl:members rdf:parseType="Collection">
		<rdf:Description rdf:about=”#Boy"/>
		<rdf:Description rdf:about="#Girl"/>
		<rdf:Description rdf:about="#Other"/>       
	 </owl:members>    </rdf:Description>
```

## 	Negative Property Assertion

OWL 2 lets us assert that an individual does not have a particular property value```<owl:NegativePropertyAssertion>  <owl:sourceIndividual rdf:resource=“john”/>  <owl:assertionProperty rdf:resource=“hasChild”/>  <owl:targetIndividual rdf:resource=“susan”/></owl:NegativePropertyAssertion>```

## Self Restriction

Define a class of individuals which are related to to themselves by a given property```<owl:Restriction>  <owl:onProperty rdf:resource=“...”/>  <owl:hasSelf rdf:datatype=“&xsd;boolean”>true</owl:hasSelf></owl:Restriction>```

```
Narcissist loves exactly himself<owl:Class rdf:about=“#Narcissist”>  <owl:equivalentClass>    <owl:Class>      <owl:intersectionOf rdf:parseType="Collection">        <owl:Class rdf:about=“#Person”/>        <owl:Restriction>          <owl:onProperty rdf:resource=“#loves”/>          <owl:cardinality>1</owl:cardinality>		     <owl:hasSelf 			  		rdf:datatype=“&xsd;boolean”>true</owl:hasSelf>        </owl:Restriction>      </owl:intersectionOf>    <owl:Class>  </owl:equivalentClass></owl:Class>```

## Qualified Cardinality(限定基数)

OWL 2 lets us specify both the local range of a property and the number of values taken by the property together:

∃ (= 4) hasPart.Wheel
```<owl:Restriction>  <owl:onProperty rdf:resource=“hasPart”/>  <owl:onClass rdf:resource=“Wheel”/>  <owl:cardinality rdf:datatype=“&xsd;integer”>4</owl:cardinality></owl:Restriction>Similar construct for datatype properties```

## Reflexive Properties

a property is globally reflexive (relates every object to itself

such a property relates everything to itself. For the following example, note that everybody as same age as himself .

```
<owl:ReflexiveProperty rdf:about=“sameAgeAs”/>
```

## irreflexive Properties

Allows us to assert that a property relates no object to itself对象没有属性与自身关联```<owl:IrreflexiveProperty rdf:about=“strictlyTallerThan”/><owl:IrreflexiveProperty rdf:about=“marriedWith”/>
```

## Assymetric Properties

Allows us to assert that a property is asymmetric:If p(x,y), then not p(y,x)<owl:AsymmetricProperty rdf:about=“strictlyTallerThan”/>

## Disjoint Properties

Allows us to state that two individuals cannot be related to each other by two different properties that have been declared disjoint```<owl:ObjectProperty rdf:about=“separatedFrom”>  <owl:propertyDisjointWithrdf:resource=“contiguousWith”/></owl:ObjectProperty>Typical examples include antonymic relationships:closeTo – farFromloves – hates (?)```

## Property Chain Inclusion

OWL2 Allow: define such property compositions```<owl:ObjectProperty rdf:about=“hasUncle”>  <owl:propertyChainAxiom rdf:parseType=“Collection”>    <owl:ObjectProperty rdf:about=“hasParent”/>    <owl:ObjectProperty rdf:about=“hasBrother”/>  </owl:propertyChainAxiom></owl:ObjectProperty>```

## Keys

define uniquely identifying keys that comprise several propertie

```<owl:Class rdf:about=“Person”>  <owl:hasKey rdf:parseType=“Collection”>    <owl:DatatypeProperty rdf:about=“hasSSN”/>    <owl:DatatypeProperty rdf:about=“birthDate”/>  </owl:hasKey></owl:Class>```

For example, the datatype of integers greater than or equal to 5:```<owl:Datatype>  <owl:onDatatype rdf:resource=“&xsd;integer”/>  <owl:withRestrictions rdf:parseType=“Collection”>    <xsd:minInclusive rdf:datatype=“&xsd;integer”>5</xsd:minInclusive>  </owl:withRestrictions></owl:Datatype>```