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
	<owl:Class rdf:about=“#Whisky”/>    <owl:Restriction>    <owl:onProperty rdf:resource=“#distilledBy”/>         				 <owl:cardinality>1</owl:cardinality>      				</owl:Restriction>      			</owl:intersectionOf>
      		<owl:Class>
      	</owl:equivalentClass>
      	</owl:Class>
	```
主语：SingleMaltWhisky 等同于 主语：whiskey 与 谓语：distilledby ,通过限制cardinality=1 进行相交得出的类

- local range constraints

	- ∃R.C, ∀R.C

- local value constraints

	- ∃R.{x}

- restriction format

```
<owl:Restriction>  <owl:onProperty rdf:resource=“property”/>  constraint expression</owl:Restriction>
```