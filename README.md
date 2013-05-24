# A Python wrapper for the Java Stanford Core NLP tools
---------------------------

This is a fork of [stanford-corenlp-python](https://github.com/dasmith/stanford-corenlp-python)

## Edited
   * Update to Stanford CoreNLP v1.3.5
   * Fix many bugs & improve performance
   * Using jsonrpclib for stability and performance
   * Can edit the constants as argument such as Stanford Core NLP directory
   * Adjust parameters not to timeout in high load
   * Fix a problem on input long texts, by Johannes Castner [stanford-corenlp-python](https://github.com/jac2130/stanford-corenlp-python)
   * Packaging

## Requirements
   * [pexpect](http://www.noah.org/wiki/pexpect)
   * [unidecode](http://pypi.python.org/pypi/Unidecode)
   * [jsonrpclib](https://github.com/joshmarshall/jsonrpclib) (optionally)

## Download and Usage

To use this program you must [download](http://nlp.stanford.edu/software/corenlp.shtml#Download) and unpack the zip file containing Stanford's CoreNLP package.  By default, `corenlp.py` looks for the Stanford Core NLP folder as a subdirectory of where the script is being run.


In other words:

    sudo pip install jsonrpclib pexpect unidecode   # unidecode is optional
    git clone https://bitbucket.org/torotoki/corenlp-python.git
	  cd corenlp-python
    wget http://nlp.stanford.edu/software/stanford-corenlp-full-2013-04-04.zip
    unzip stanford-corenlp-full-2013-04-04.zip

Then, to launch a server:

    python corenlp/corenlp.py

Optionally, you can specify a host or port:

    python corenlp/corenlp.py -H 0.0.0.0 -p 3456

That will run a public JSON-RPC server on port 3456.
And you can specify Stanford CoreNLP directory:

    python corenlp/corenlp.py -S stanford-corenlp-full-2013-04-04/


Assuming you are running on port 8080 and CoreNLP directory is `stanford-corenlp-full-2013-04-04/` in current directory, the code in `client.py` shows an example parse:

    import jsonrpclib
    from simplejson import loads
    server = jsonrpclib.Server("http://localhost:8080")

    result = loads(server.parse("Hello world.  It is so beautiful"))
    print "Result", result

That returns a dictionary containing the keys `sentences` and (when applicable) `corefs`. The key `sentences` contains a list of dictionaries for each sentence, which contain `parsetree`, `text`, `tuples` containing the dependencies, and `words`, containing information about parts of speech, NER, etc:

	{u'sentences': [{u'parsetree': u'(ROOT (S (VP (NP (INTJ (UH Hello)) (NP (NN world)))) (. !)))',
	                 u'text': u'Hello world!',
	                 u'tuples': [[u'dep', u'world', u'Hello'],
	                             [u'root', u'ROOT', u'world']],
	                 u'words': [[u'Hello',
	                             {u'CharacterOffsetBegin': u'0',
	                              u'CharacterOffsetEnd': u'5',
	                              u'Lemma': u'hello',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'UH'}],
	                            [u'world',
	                             {u'CharacterOffsetBegin': u'6',
	                              u'CharacterOffsetEnd': u'11',
	                              u'Lemma': u'world',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'NN'}],
	                            [u'!',
	                             {u'CharacterOffsetBegin': u'11',
	                              u'CharacterOffsetEnd': u'12',
	                              u'Lemma': u'!',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'.'}]]},
	                {u'parsetree': u'(ROOT (S (NP (PRP It)) (VP (VBZ is) (ADJP (RB so) (JJ beautiful))) (. .)))',
	                 u'text': u'It is so beautiful.',
	                 u'tuples': [[u'nsubj', u'beautiful', u'It'],
	                             [u'cop', u'beautiful', u'is'],
	                             [u'advmod', u'beautiful', u'so'],
	                             [u'root', u'ROOT', u'beautiful']],
	                 u'words': [[u'It',
	                             {u'CharacterOffsetBegin': u'14',
	                              u'CharacterOffsetEnd': u'16',
	                              u'Lemma': u'it',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'PRP'}],
	                            [u'is',
	                             {u'CharacterOffsetBegin': u'17',
	                              u'CharacterOffsetEnd': u'19',
	                              u'Lemma': u'be',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'VBZ'}],
	                            [u'so',
	                             {u'CharacterOffsetBegin': u'20',
	                              u'CharacterOffsetEnd': u'22',
	                              u'Lemma': u'so',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'RB'}],
	                            [u'beautiful',
	                             {u'CharacterOffsetBegin': u'23',
	                              u'CharacterOffsetEnd': u'32',
	                              u'Lemma': u'beautiful',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'JJ'}],
	                            [u'.',
	                             {u'CharacterOffsetBegin': u'32',
	                              u'CharacterOffsetEnd': u'33',
	                              u'Lemma': u'.',
	                              u'NamedEntityTag': u'O',
	                              u'PartOfSpeech': u'.'}]]}],
	u'coref': [[[[u'It', 1, 0, 0, 1], [u'Hello world', 0, 1, 0, 2]]]]}

Not to use JSON-RPC, load the module instead:

    from corenlp import StanfordCoreNLP
    corenlp_dir = "stanford-corenlp-full-2013-04-04/"
    corenlp = StanfordCoreNLP(corenlp_dir)  # wait a few minutes...
    corenlp.parse("Parse it")

If you need to parse long texts (more than 30-50 sentences), you have to use a batch_parse() function. It reads text files from input directory and returns a generator object of dictionaries parsed each file results:

    from corenlp import batch_parse
    corenlp_dir = "stanford-corenlp-full-2013-04-04/"
    raw_text_directory = "sample_raw_text/"
    parsed = batch_process(raw_text_directory, corenlp_dir)  # It returns a generator object
    print parsed  #=> [{'coref': ..., 'sentences': ..., 'file_name': 'new_sample.txt'}]

## Developer
   * Hiroyoshi Komatsu [hiroyoshi.komat@gmail.com]
   * Johannes Castner [jac2130@columbia.edu]

## Related Projects

These two projects are python wrappers for the [Stanford Parser](http://nlp.stanford.edu/software/lex-parser.shtml), which includes the Stanford Parser, although the Stanford Parser is another project.
  - [stanford-parser-python](http://projects.csail.mit.edu/spatial/Stanford_Parser) uses [JPype](http://jpype.sourceforge.net/) (interface to JVM)
  - [stanford-parser-jython](http://blog.gnucom.cc/2010/using-the-stanford-parser-with-jython/) uses Python
