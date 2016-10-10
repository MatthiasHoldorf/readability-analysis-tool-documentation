
# Table of content
- [About](#id-section01)
- [Getting Started](#id-section02)
- [Arguments](#id-section0)
- [Readability Formulas](#id-section1)
- [Readability Statistics](#id-section2)
- [Readability Anomalies](#id-section3)
- [Configuration & Quality Gate](#id-section4)

<div id='id-section01'/>
# About

RAT is a tool to detect readability anomalies in text based on readability rules.

Readability anomalies describe findings in a text which are difficult to read. The principle is similar to bug pattern in static code analysis. An example constitutes the following readability rule that detects consecutive fillers in a sentence:

```Java
@TypeCapability(inputs = { "de.tudarmstadt.ukp.dkpro.core.api.segmentation.type.Token",
        "de.tudarmstadt.ukp.dkpro.core.api.segmentation.type.Sentence" }, outputs = {
                "de.qaware.rat.type.RatReadabilityAnomaly" })
public class ConsecutiveFillersAnnotator extends JCasAnnotator_ImplBase {
    private static final Logger LOGGER = LoggerFactory.getLogger(ConsecutiveFillersAnnotator.class);

    public static final String SEVERITY = RuleParameter.SEVERITY;
    @ConfigurationParameter(name = RuleParameter.SEVERITY, mandatory = true, defaultValue = "Minor")
    protected String severity;

    @Override
    public void process(JCas aJCas) {
        LOGGER.info("Start ConsecutiveFillersAnnotator");

        try {
            String[] fillers = ImporterUtils.readWordlist("word-lists/Fillers.txt");
            List<Token> words = TextStatistic.getWordsInDocument(aJCas);

            int maxSize = words.size() - 1;
            for (int i = 0; i < words.size(); i++) {

                if (i + 1 <= maxSize) {
                    if (Arrays.asList(fillers).contains(words.get(i).getCoveredText().toLowerCase())
                            && Arrays.asList(fillers).contains(words.get(i + 1).getCoveredText().toLowerCase())) {
                        List<String> violations = new ArrayList<String>();
                        violations.add(words.get(i).getCoveredText());
                        violations.add(words.get(i + 1).getCoveredText());

                        UimaUtils.createRatReadabilityAnomaly(aJCas, "ConsecutiveFillers", "ReadabilityAnomaly",
                                severity,
                                "Vermeiden Sie aufeinanderfolgende Füllwörter. ("
                                        + CollectionUtils.printStringList(violations) + ")",
                                violations, words.get(i).getBegin(), words.get(i).getEnd());
                    }
                }
            }
        } catch (IOException e) {
            LOGGER.error("The ConsecutiveFillersAnnotator failed.");
        }
    }
}
```

During an analysis a .docx file is enriched with comments (the readability anomaly findings). The .docx file is then saved as a new file with a "-rat.docx" suffix. This ensures that the original document cannot be corrupted by RAT. In case a document is analysed that already has a "-rat.docx" suffix, the [b]very same[/b] document is altered.

![Results](/doc-images/results-docx.PNG)

Additionaly, RAT computes a report about statistics of the text (e.g., average words per sentence, reading time, most used nouns) and [readability formulas](https://en.wikipedia.org/wiki/Readability#Popular_readability_formulas) and stores them in an HTML report next to the analyzed document.

For both the analysed document and the statstic report an optional outputDirectorycan be specified via the commands "-o" or "--outputDirectory".

The HTML report aggregates all readability mesaurements and assess the overall readability of the text through a quality gate; similar to static code analysis tools.

Further, the report shows anomalies that were marked as declined (false positives) or incorporated by the user. RAT saves these information also in the XML of the .docx file itself. This way one can comprehend what action where taken during the editing of the document.

Name | Description
------------ | -------------
RAT | Readability Analysis Tool (Readability-Checker)
Supported File Types | .docx
Readability Rules | German
Features | <ul><li>Annotation of Readability Anomalies</li><li>Statistic Report</li><li>Configurable Anomaly Rules</li><li>Configurable Quality Gate</li><li>Detection of False Positives</li></ul>
Performance | 6 Seconds for 10.000 Words (45 Pages) after initialization
Technologies | <ul><li>UIMA</li><li>UIMA Ruta</li><li>DKPro Core</li></ul>
Licence | Not determined

<div id='id-section02'/>
# Getting Started

RAT is invoked via the command line. The assembly is delivered with example exatuables and documents. By that, the handling of the software can be learned quick and the results are shown.

<pre>
.
|---config
    |   rat-config.xml
|---examples
    |---config-in-folder
        |   45-page-9500-words-assignment.docx
        |   rat-config.xml
        |   rat-example.cmd
    |   multiple-files
        |   45-page-9500-words-assignment-0.docx
        |   45-page-9500-words-assignment-1.docx
        |   45-page-9500-words-assignment-2.docx
        |   rat-example.cmd
    |   output-directory
    |   single-file
        |   45-page-9500-words-assignment.docx
        |   rat-example.cmd
|---lib
    |   # jar files of the application
|   rat.cmd
|   rat.sh
|   rat.example.cmd
|   rat.example.sh
</pre>

<div id='id-section0'/>
# Arguments

RAT can be invoked from the command line with the following optional arguments:

```
usage: Rat v0.4
 -c,--configurationPath <arg>   the file path of the configuration
 -o,--outputDirectory <arg>     the output directory for the document and statistic report
 -h,--help                      display help menu
```

The last argument must be a valid path to a potential file for an analysis. Command line wildcards can be used, e.g. `/*.docx` or `directoryPath/*/*.docx`.

<div id='id-section1'/>
# Readability Formulas

RAT computes the below listed readability formulas. 

The results are saved in the same directory as the original document appneded by a "rat-report.html" suffix, e.g. "{filename}-rat-report.html".

Readability Formula | Link
------------ | -------------
Flesch | https://de.wikipedia.org/wiki/Lesbarkeitsindex#Flesch-Reading-Ease
Wiener Sachtextformel | https://de.wikipedia.org/wiki/Lesbarkeitsindex#Wiener_Sachtextformel

<div id='id-section2'/>
# Readability Statistics

RAT computes the below listed readability statistics. 

The results are saved in the same directory as the original document with a "rat-report.html" suffix, e.g. "{filename}-rat-report.html".

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
12	| Most used Nouns | 
13	| Most used Verbs | 
14	| Most used Adjectives | 
15	| Most used Conjunctions | 
16	| Percentage of Nouns in Text | 
17	| Percentage of Verbs in Text | 
18	| Percentage of Adjectives in Text | 
19	| Percentage of Conjunctions in Text | 
20	| Percentage of specified keywords in configuration file | 

<div id='id-section3'/>
# Readability Anomalies

RAT detects the below listed readability anomalies and annotates them as comments in .docx files.

The annotated .docx file is saved in the same directory as the original document with a "-rat.docx" suffix, e.g. "{filename}-rat.{file-extension}".

## Implemented Readability Anomalies
- AdjectiveStyle
- AmbiguousAdjectivesAndAdverbs
- ConsecutiveFillers
- ConsecutivePrepositions
- DoubleNegative
- Filler
- FillerSentence
- IndirectSpeech
- LeadingAttributes
- LongSentence
- LongWord
- [ModalVerb](#modalVerb)
- ModalVerbSentence
- NestedSentence
- NestedSentenceConjunction
- NestedSentenceDelimiter
- [NominalStyle](#nominalStyle)
- PassiveVoice
- SentencesStartWithSameWord
- SubjectiveLanguage
- UnnecessarySyllables

<div id='modalVerb'/>

Key | Properties
------------ | -------------
Anomaly Name | ModalVerb
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

The default readability rule configuration:

```xml
<anomalies>
    <anomaly-rule>
        <name>AdjectiveStyle</name>
        <severity>Major</severity>
        <threshold>6</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>AmbiguousAdjectivesAndAdverbs</name>
        <severity>Minor</severity>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>ConsecutiveFillers</name>
        <severity>Minor</severity>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>ConsecutivePrepositions</name>
        <severity>Minor</severity>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>DoubleNegative</name>
        <severity>Major</severity>
        <threshold>2</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>Filler</name>
        <severity>Minor</severity>
        <enabled>false</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>FillerSentence</name>
        <severity>Major</severity>
        <threshold>3</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>IndirectSpeech</name>
        <severity>Minor</severity>
        <enabled>false</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>LeadingAttributes</name>
        <severity>Major</severity>
        <threshold>4</threshold>
        <enabled>false</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>LongSentence</name>
        <severity>Critical</severity>
        <threshold>35</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>LongWord</name>
        <severity>Major</severity>
        <threshold>8</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>ModalVerb</name>
        <severity>Minor</severity>
        <enabled>false</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>ModalVerbSentence</name>
        <severity>Minor</severity>
        <threshold>2</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>NestedSentence</name>
        <severity>Critical</severity>
        <threshold>6</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>NestedSentenceConjunction</name>
        <severity>Major</severity>
        <threshold>3</threshold>
        <enabled>false</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>NestedSentenceDelimiter</name>
        <severity>Major</severity>
        <threshold>3</threshold>
        <enabled>false</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>NominalStyle</name>
        <severity>Major</severity>
        <threshold>3</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>PassiveVoice</name>
        <severity>Major</severity>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>SentencesStartWithSameWord</name>
        <severity>Minor</severity>
        <threshold>2</threshold>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>SubjectiveLanguage</name>
        <severity>Minor</severity>
        <enabled>true</enabled>
    </anomaly-rule>
    <anomaly-rule>
        <name>UnnecessarySyllables</name>
        <severity>Minor</severity>
        <enabled>true</enabled>
    </anomaly-rule>
</anomalies>
```

<div id='id-section4'/>
# Configuration & Quality Gate

The generated report "{filename}-rat-report.html" aggregates the results of the different measurements (formulas, statistics and anomalies). Further, a quality gate indicates whether the text is too trivial or too hard to understand.

First, RAT looks for the configuration at the provided argument (-c or --configurationPath). If this parameter is not provided, e.g. is null, or there is no valid file at the location, RAT will look in the directory path of the file that is currently analysed for a file named "rat-config.xml". If both ways fail to obtain a configuration file, the defaultConfig parameter provided by the executor is considered. In case the default configuration is not a file (e.g., is deleted), the rat internal configuration will be loaded.

The default quality gate configuration:

```xml
<quality-gate>
        <anomalies>
            <anomaly>
                <severity>minor</severity>
                <warning-threshold>50</warning-threshold>
                <error-threshold>9999</error-threshold>
            </anomaly>
            <anomaly>
                <severity>major</severity>
                <warning-threshold>1</warning-threshold>
                <error-threshold>30</error-threshold>
            </anomaly>
            <anomaly>
                <severity>critical</severity>
                <warning-threshold>1</warning-threshold>
                <error-threshold>1</error-threshold>
            </anomaly>
        </anomalies>
        <formulas>
            <formula>
                <name>flesch-reading-ease-amstad</name>
                <easy-warning-threshold>75</easy-warning-threshold>
                <hard-warning-threshold>25</hard-warning-threshold>
                <easy-error-threshold>80</easy-error-threshold>
                <hard-error-threshold>20</hard-error-threshold>
            </formula>
            <formula>
                <name>wiener-sachtextformel</name>
                <easy-warning-threshold>7</easy-warning-threshold>
                <hard-warning-threshold>14</hard-warning-threshold>
                <easy-error-threshold>5</easy-error-threshold>
                <hard-error-threshold>16</hard-error-threshold>
            </formula>
        </formulas>
        <statistics>
            <statistic>
                <name>average-number-of-words-per-sentence</name>
                <easy-warning-threshold>7</easy-warning-threshold>
                <hard-warning-threshold>19</hard-warning-threshold>
                <easy-error-threshold>5</easy-error-threshold>
                <hard-error-threshold>26</hard-error-threshold>
            </statistic>
            <statistic>
                <name>average-number-of-syllables-per-word</name>
                <easy-warning-threshold>1.4</easy-warning-threshold>
                <hard-warning-threshold>3</hard-warning-threshold>
                <easy-error-threshold>1.2</easy-error-threshold>
                <hard-error-threshold>4</hard-error-threshold>
            </statistic>
        </statistics>
    </quality-gate>
```
