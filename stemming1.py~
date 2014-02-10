import re

from sklearn import svm
from numpy import *
# Suffix replacement lists





_step2list = {
              "ational": "ate",
              "tional": "tion",
              "enci": "ence",
              "anci": "ance",
              "izer": "ize",
              "bli": "ble",
              "alli": "al",
              "entli": "ent",
              "eli": "e",
              "ousli": "ous",
              "ization": "ize",
              "ation": "ate",
              "ator": "ate",
              "alism": "al",
              "iveness": "ive",
              "fulness": "ful",
              "ousness": "ous",
              "aliti": "al",
              "iviti": "ive",
              "biliti": "ble",
              "logi": "log",          
              }

_step3list = {
              "icate": "ic",
              "ative": "",
              "alize": "al",
              "iciti": "ic",
              "ical": "ic",
              "ful": "",
              "ness": "",          
              }


_cons = "[^aeiou]"
_vowel = "[aeiouy]"
_cons_seq = "[^aeiouy]+"
_vowel_seq = "[aeiou]+"

# m > 0
_mgr0 = re.compile("^(" + _cons_seq + ")?" + _vowel_seq + _cons_seq)
# m == 0
_meq1 = re.compile("^(" + _cons_seq + ")?" + _vowel_seq + _cons_seq + "(" + _vowel_seq + ")?$")
# m > 1
_mgr1 = re.compile("^(" + _cons_seq + ")?" + _vowel_seq + _cons_seq + _vowel_seq + _cons_seq)
# vowel in stem
_s_v = re.compile("^(" + _cons_seq + ")?" + _vowel)
# ???
_c_v = re.compile("^" + _cons_seq + _vowel + "[^aeiouwxy]$")

# Patterns used in the rules

_ed_ing = re.compile("^(.*)(ed|ing)$")
_at_bl_iz = re.compile("(at|bl|iz)$")
_step1b = re.compile("([^aeiouylsz])\\1$")
_step2 = re.compile("^(.+?)(ational|tional|enci|anci|izer|bli|alli|entli|eli|ousli|ization|ation|ator|alism|iveness|fulness|ousness|aliti|iviti|biliti|logi)$")
_step3 = re.compile("^(.+?)(icate|ative|alize|iciti|ical|ful|ness)$")
_step4_1 = re.compile("^(.+?)(al|ance|ence|er|ic|able|ible|ant|ement|ment|ent|ou|ism|ate|iti|ous|ive|ize)$")
_step4_2 = re.compile("^(.+?)(s|t)(ion)$")
_step5 = re.compile("^(.+?)e$")

# Stemming function

def stem(w):
    """Uses the Porter stemming algorithm to remove suffixes from English
    words.
    
    >>> stem("fundamentally")
    "fundament"
    """
    
    if len(w) < 3: return w
    
    first_is_y = w[0] == "y"
    if first_is_y:
        w = "Y" + w[1:]
        
    # Step 1a
    if w.endswith("s"):
        if w.endswith("sses"):
            w = w[:-2]
        elif w.endswith("ies"):
            w = w[:-2]
        elif w[-2] != "s":
            w = w[:-1]
    
    # Step 1b
    
    if w.endswith("eed"):
        s = w[:-3]
        if _mgr0.match(s):
            w = w[:-1]
    else:
        m = _ed_ing.match(w)
        if m:
            stem = m.group(1)
            if _s_v.match(stem):
                w = stem
                if _at_bl_iz.match(w):
                    w += "e"
                elif _step1b.match(w):
                    w = w[:-1]
                elif _c_v.match(w):
                    w += "e"
            
    # Step 1c
    
    if w.endswith("y"):
        stem = w[:-1]
        if _s_v.match(stem):
            w = stem + "i"
            
    # Step 2
    
    m = _step2.match(w)
    if m:
        stem = m.group(1)
        suffix = m.group(2)
        if _mgr0.match(stem):
            w = stem + _step2list[suffix]
            
    # Step 3
    
    m = _step3.match(w)
    if m:
        stem = m.group(1)
        suffix = m.group(2)
        if _mgr0.match(stem):
            w = stem + _step3list[suffix]

    # Step 4
    
    m = _step4_1.match(w)
    if m:
        stem = m.group(1)
        if _mgr1.match(stem):
            w = stem
    else:
        m = _step4_2.match(w)
        if m:
            stem = m.group(1) + m.group(2)
            if _mgr1.match(stem):
                w = stem
    
    # Step 5
    
    m = _step5.match(w)
    if m:
        stem = m.group(1)
        if _mgr1.match(stem) or (_meq1.match(stem) and not _c_v.match(stem)):
            w = stem
    
    if w.endswith("ll") and _mgr1.match(w):
        w = w[:-1]
    
    if first_is_y:
        w = "y" + w[1:]

    return w


def trainNB0(trainMatrix,trainCategory):
	numTrainDocs = len(trainMatrix)
	numWords = len(trainMatrix[0])
	pAbusive = sum(trainCategory)/float(numTrainDocs)
	#lamba correction
	p0Num = ones(numWords); p1Num = ones(numWords)
	p0Denom = 2.0; p1Denom = 2.0
	
	for i in range(numTrainDocs):
		if trainCategory[i] == 1:
			p1Num += trainMatrix[i]
			p1Denom += sum(trainMatrix[i])
		else:
			p0Num += trainMatrix[i]
			p0Denom += sum(trainMatrix[i])
			p1Vect = p1Num/p1Denom #change to log()
			p0Vect = p0Num/p0Denom #change to log()
	return p0Vect,p1Vect,pAbusive


def classifyNB(vec2Classify, p0Vec, p1Vec, pClass1):
	p1 = sum(vec2Classify * p1Vec) + log(pClass1)
	p0 = sum(vec2Classify * p0Vec) + log(1.0 - pClass1)
	if p1 > p0:
		return 1
	else:
		return 0

#Main function starts here

def printOutputFiles(ftest, fout, wordList,p0V,p1V,pAb,path):
	latLong=[]
	feature_vector=[]
	for each_word in range(len(wordList)):
		feature_vector.append(0)
	for line in ftest:
		line = line.rstrip()
		params=line.split("~~~")
		#testLines.append(params[0])
		params[0] = params[0].replace('~','')
		words=params[0].split()
		for each_word in words:
			if stem(each_word) in wordList:
				feature_vector[wordList.index(stem(each_word))]+=1  #for basic
	
		if  params[1]=="0,0":
			fout.write(path+"~"+params[0]+"~"+str(classifyNB(array(feature_vector),p0V,p1V,pAb))+"~"+'0~0'+"~"+str(params[2])+"~"+str(params[3])+"~"+str(params[4])+"\n")
		else:
			latitude=params[1].split(",")[0]
			longitude=params[1].split(",")[1]
			latLongPair=latitude+"~"+longitude
			fout.write(path+"~"+params[0]+"~"+str(classifyNB(array(feature_vector),p0V,p1V,pAb))+"~"+latLongPair+"~"+str(params[2])+"~"+str(params[3])+"~"+str(params[4])+"\n")
			
		feature_vector=[]
		for each_word in range(len(wordList)):
			feature_vector.append(0)
	

positive_path="./rt-polarity.neg"
negative_path="./rt-polarity.pos"

stopwords_path="./stop_words.txt"

fstop = open(stopwords_path)
fpos = open(positive_path)
fneg = open(negative_path)


stopWords=[]
words=[]
wordList=[]
wordDic={}

for line in fstop:
	stopWords.append(line)


for line in fpos:
	words=line.split()
	for each_word in words:
		if each_word not in stopWords:
			if wordDic.__contains__(stem(each_word)):
				wordDic[stem(each_word)][0]+=1			
			  		
			else:
				wordDic[stem(each_word)]=[]
				wordDic[stem(each_word)].append(1)
				wordDic[stem(each_word)].append(0)
	

for line in fneg:
	words=line.split()
	for each_word in words:
		if each_word not in stopWords:
			if wordDic.__contains__(stem(each_word)):
				wordDic[stem(each_word)][1]+=1			
					
			else:
				wordDic[stem(each_word)]=[]
				wordDic[stem(each_word)].append(0)
				wordDic[stem(each_word)].append(1)  	


#form the feature vector

 
wordList=[]
for each_word in wordDic:
	wordList.append(each_word)


#print wordList

featureVectorList=[]
feature_vector=[]

for each_word in range(len(wordDic)):
	feature_vector.append(0)


label_vector=[]

fpos = open(positive_path)
fneg = open(negative_path)

for line in fneg:
	words=line.split()
	
	for each_word in words:
		feature_vector[wordList.index(stem(each_word))]+=1  #for basic
	
	featureVectorList.append(feature_vector)
	feature_vector=[]
	for each_word in range(len(wordDic)):
		feature_vector.append(0)
	label_vector.append(0)




#print featureVectorList
for line in fpos:
	words=line.split()
	for each_word in words:
		feature_vector[wordList.index(stem(each_word))]+=1  #for basic
	featureVectorList.append(feature_vector)
	feature_vector=[]
	for each_word in range(len(wordDic)):
		feature_vector.append(0)
	label_vector.append(1)


#print featureVectorList


p0V,p1V,pAb = trainNB0(array(featureVectorList),array(label_vector))
path = raw_input("Enter Name of input test file:")
ftest1 =  open(path)
'''
ftest2 =  open("./wolfofwallstreet")
ftest3 =  open("./TheMonumentsMen")
ftest4 =  open("./AmericanHustle")
ftest5 =  open("./12YearsASlave")
ftest6 =  open("./RideAlong")
'''

fout1 = open("classified_"+path,"w")

'''
fout2 = open("outputWolfOfWallStreet","w")
fout3 = open("outputMonumentsMen","w")
fout4 = open("outputAmericanHustle","w")
fout5 = open("output12YearsASlave","w")
fout6 = open("outputRideAlong","w")
'''


printOutputFiles(ftest1,fout1, wordList,p0V,p1V,pAb,path)
#printOutputFiles(ftest2,fout2, wordList,p0V,p1V,pAb)
#printOutputFiles(ftest3,fout3, wordList,p0V,p1V,pAb)
#printOutputFiles(ftest4,fout4, wordList,p0V,p1V,pAb)
#printOutputFiles(ftest5,fout5, wordList,p0V,p1V,pAb)
#printOutputFiles(ftest6,fout6, wordList,p0V,p1V,pAb)
