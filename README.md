Gephi-scripts
=============

Custom scripts to move data into gephi

#gephi_data_formatter.py
#Take data from:    [Location]  [Name1]     [Name2]         [Name3]
 
# (1.) Create a reference list/file:    [Name1] = 1
#                                       [Name2] = 2
#                                       [Name3] = 3
 
# (2.) Reformat the Data to:  [Location]  1   2
#                             [Location]  1   3
#                             [Location]  2   3
 
 
#---Modules----
 
import csv
 
#---Constants-----
 
NUMLINES = 4765
LONGESTLIST = 22
NUMMERCHANTS = 15000
BLANKDATA = -1
 
#---CSV Reader/Writer Objects-------
 
dataInput = csv.reader(open("barbados_input.csv"))
 
refOutput = csv.writer(open("barbados_reference.csv", 'wb'))
 
dataOutput = csv.writer(open("barbados_output.csv", 'wb'))
 
 
 
#---Create Lists of Data------
 
#sets the individual lists first, the list-of-lists 2nd
#but the data is read [listID][itemID]
nameSet = [[-1 for x in xrange(LONGESTLIST)] for x in xrange(NUMLINES)]
 
for i in range(0, NUMLINES):
 
    rowList = dataInput.next()
 
    for j in range(0, len(rowList)):
        nameSet[i][j] = rowList[j]
    #end for j
 
 
#---Test Print ----
 
print "Input Data Set:"
for i in range(0, NUMLINES):
    for j in range(0, LONGESTLIST):
        print nameSet[i][j],
    #end for j
 
    print
print
#end for i
         
 
#---Create Data Set and Reference Data------
 
dataSet = [[-1 for x in xrange(LONGESTLIST)] for x in xrange(NUMLINES)]
 
refList = [[-1 for x in xrange(3)] for x in xrange(NUMMERCHANTS)]
 
for i in range(0, NUMLINES):
    for j in range(1, LONGESTLIST):
 
        if ((nameSet[i][j] != BLANKDATA) and (nameSet[i][j] != '')):
 
            check = 0
 
            while ( (refList[check][1] != nameSet[i][j]) and (refList[check][1]!= BLANKDATA) ):
                check = check + 1
            #end while
 
            if (refList[check][1] == BLANKDATA) :
                refList[check][0] = nameSet [i][0]
                refList[check][1] = nameSet [i][j]
                refList[check][2] = check
                refOutput.writerow(refList[check])
            #end if
 
            dataSet[i][j-1] = check
 
        #end if
    #end for j
#end for i
 
#---Test Print Reference and Data Sets-----
 
print "Reference List:"
for i in range(0,NUMMERCHANTS):
    print refList[i]
#end for i
 
print
print "Data Set:"
for i in range(0, NUMLINES):
    print dataSet[i]
#end for i
 
 
#---Create Output Data-------
numEdges = 0
for group in range(0,NUMLINES):
 
    for i in range(0, (len(dataSet[group])-1)):
        for j in range( (i+1), len(dataSet[group])):
 
            if ((dataSet[group][i] != BLANKDATA) and (dataSet[group][j]) != BLANKDATA):
                numEdges = numEdges + 1
                newRow = [ dataSet[group][i], dataSet[group][j], numEdges, 1 ]
                print newRow
                dataOutput.writerow( newRow )        
 
        #end for j
    #end for i
#end for group
