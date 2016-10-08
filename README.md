
# Table of content
- [About](#id-section01)
- [Getting Started](#id-section02)
- [Arguments](#id-section0)
- [Readability Formulas](#id-section1)
- [Readability Statistics](#id-section2)
- [Readability Anomalies](#id-section3)
- [Configuration & Quality Gate](#id-section4)
- [ToDo](#id-section5)

<div id='id-section01'/>
# About

RAT is a tool to detect readability anomalies (i.e., text passages that are hard for a reader to understand). These readability anomalies are annotated (as comments) in a .docx file. Readability rules writte in UIMA Ruta or plain Java detect the readability anomalies.

Further, RAT computes statistics about the text (e.g., average words per sentence) and readability formulas (e.g., Flesch) and stores them in an HTML report.

The HTML report aggregates all readability mesaurements and assess the overall readability of the text through a quality gate, similar to static code analysis tools.

Name | Description
------------ | -------------
RAT | Readability Analysis Tool (Readability-Checker)
Supported File Types | .docx
Readability Rules | German, English
Features | <ul><li>Annotation of Readability</li><li>Anomalies as Comments</li><li>HTML Report</li><li>Configurable Anomaly Rules</li><li>Configurable Quality Gate</li><li>Detection of False Positives</li><li>Fast (e.g., 6 Seconds for 10.000 Words (45 Pages))</li></ul>
Technologies | <ul><li>UIMA</li><li>UIMA Ruta</li><li>DKPro Core</li></ul>
Licence | ALV2

<div id='id-section02'/>
# Getting Started

RAT is invoked through the command line. It takes a file or directory path as an argument and generates a second .docx file
"{filename}-rat.{file-extension}" with the annotations of readability anomalies. The second file generated is the report "{filename}-rat-report.html".

[IMAGE-DIRECTORY-LAYOUT]

<div id='id-section0'/>
# Arguments

RAT can be invoked from the command line with the following arguments:

```
usage: Rat v ${project.version}
 -c,--configurationPath <arg>   the file path of the configuration
 -d,--directoryPath <arg>       the directory path of the document(s) to
                                be analysed
 -h,--help                      display help menu
 -p,--filePath <arg>            the file path of the document to be
                                anaylsed
 -v,--version                   display the verison of rat
```

<div id='id-section1'/>
# Readability Formulas

RAT computes the below listed readability formulas. 

The results are saved in the same directory as the original document with a "rat-report" suffix, e.g. "{filename}-rat-report.html".

Readability Formula | Link
------------ | -------------
Flesch | https://de.wikipedia.org/wiki/Lesbarkeitsindex#Flesch-Reading-Ease
Wiener Sachtextformel | https://de.wikipedia.org/wiki/Lesbarkeitsindex#Wiener_Sachtextformel

<div id='id-section2'/>
# Readability Statistics

RAT computes the below listed readability statistics. 

The results are saved in the same directory as the original document with a "rat-report" suffix, e.g. "{filename}-rat-report.html".

 # | Name of statistic | Description
------------ | -------------  | ------------- 
1	| Reading Time | Based on 255 words per minute
2	| Speaking Time | Based on 125 words per minute
3	| Number of Sentences | 
4	| Number of Words | 
5	| Number of Syllables | 
6	| Number of Characters | 
7	| Average Number of Words per Sentence | 
8	| Average Number of Syllables per Sentence | 
9	| Average Number of Characters per Sentence | 
10	| Average Number of Syllables per Word | 
11	| Average Number of Characters per Word | 
12	| Longest Sentence | 
13	| Longest Word by Syllables | 
14	| Longest Word by Characters | 

<div id='id-section3'/>
# Readability Anomalies

RAT detects the below listed readability anomalies and marks them as comments in .docx files. It furhter displays and marks the anomalies in the "{filename}-rat-report.html".

The annotated .docx file is saved in the same directory as the original document with a "-rat" suffix, e.g. "{filename}-rat.{file-extension}".

## Implemented Readability Anomalies
- [ModalVerb](#modalVerb)
- [NominalStyle](#nominalStyle)
1.	Subjective Language
2.	Ambiguous Adverbs and Adjectives
3.	Loopholes
4.	Open-ended, non-verifiable terms
5.	Superlatives
6.	Comparatives
7.	Negative Statements
8.	Vague Pronouns
9.	Incomplete References


<div id='modalVerb'/>

Key | Properties
------------ | -------------
Anomaly Name | ModalVerb
Category | General
Severity | Major
Entity | Word
Explanation | Modalverben: können, sollen, wollen, mögen, dürfen. Mit ihnen lassen sich kritische Aussagen abschwächen – schließlich soll einen hinterher keiner festnageln können.
Incorrect Example | Wir *__sollten__* das Produkt bis zum Ende des Jahres fertig entwickelt haben.
Correct Example | Wir *__werden__* das Produkt bis zum Ende des Jahres fertiggestellt haben.

<div id='nominalStyle'/>

Key | Properties
------------ | -------------
Anomaly Name | NominalStyle
Category | General
Severity | Critical
Entity | POS, Lemma
Explanation | Sätze im Nominalstil sind durch Nomen (Substantive) und Substantivierungen geprägt. Die Verwendung entsprechender Begriffe ist nicht per se falsch oder unschön – es kommt vielmehr auf die Häufung an. Werden Sätze oder gar ganze Texte mit Substantivierungen im Stil von „das Hervorrufen“, „das Aufzeigen“ oder „die Verursachung“ übersät, leidet die Lesbarkeit.
Incorrect Example | Folglich stiegen die *__Hoffnungen__*, dass die Bewältigung der kommenden *__Herausforderungen__* und die *__Anpassung__* der *__Wirtschaftsordnung__* an die veränderten *__Rahmenbedingungen__* des globalen Wettbewerbs gelingen würden.
Correct Example | Folglich stieg die *__Hoffnung__*, dass es gelingen würde, die kommenden Probleme zu lösen und die Wirtschaft den veränderten *__Bedingungen__*  des globalen Wettbewerbs anzupassen. 

<div id='id-section4'/>
# Configuration & Quality Gate

The generated report "{filename}-rat-report.html" aggregates the results of the different measurements (formulas, statistics and anomalies). Further, a quality gate indicates whether the text is too trivial or too hard to understand.

[IMAGE DASHBOARD]

If no configuration path is provided as an argument, RAT will search in the same directory as the original document for a file named "rat-config.xml". If this does not exist, RAT will take the default configuration settings for the analysis and the quality gate.

[CONFIG.XML]

<div id='id-section5'/>
# ToDo
- [x] Explain the command line arguments
- [ ] Explain how to use the Rat
- [ ] Explain configuration of Readability Rules / Quality Gate
- [ ] Explain its API / Link to the Javadoc
- [ ] Explain how to write on new Readability Rules
- [ ] Explain how to contribute, e.g. write a module for another file input and out
- [ ] State its Performance
