# Functional_Programming_2021

The Work I have done in my second year of Computer Science in 2021, you can find many useful stuffs

Official course link : https://gitlab.epfl.ch/lamp/cs210

## How To Use

1) in Exercises you can find all 11 exercise sets with their solutions
2) in Labs, you can find all 9 labs with my solutions
3) in Past2020Exam, you can find all my implementations of the 2020 final exam
4) in Previous exams you can find all the previous exams
5) you can find the slides

## How To Get A Good Grade ?

### For the midterm :
If some topics were not covered but were in previous midterm, for god's sake dont learn them !!! 
I got 30% on my midterm because I spent too much time on stuffs that we were not supposed to revise !
1) Do all exercise sets that are RELEVANT to the midterm at least 4 times.
2) Do harder exercises on variance such as midterm of 2021 (this one more than 80% of students didn't find the correct answer ! )
3) Pray God
4) Revise other midterms
5) Type 99 scala problems and do them : http://aperiodic.net/phil/scala/s-99/


### For the final :
1) Do all exercise sets 4 times
2) Do all past exams 3 times
3) You need to be able to spend less than 1 minute on easy questions (eg: given ... using ... with ... extension...)
4) You need to be very fast on induction questions
5) You need to know all methods that are in the appendix and how to use them
6) Do not mess up with syntaxes. eg: #:: when you need to use :: or #:::
7) Remember, this is functional programming, generally the answers are less than 5-6 lines
8) If tail recursive is not asked, do not try a tail recursive implementation. You are already in hardcore mode, don't make it harder, you are not a half-god.
9) Try to use for yield if possible, sometimes you can get an answer in 4 lines.
10) Use PatternMatching as much as possible. If you need to get the second element of a stream, do 
x match 
case y#::z#::tail 
case y #:: tail
case Stream.Empty

You can also do pattern matching on 2 lists at the same time (x, y) match
or turn strings into list[Char] by typing .toList

11) Bring your glasses the exam day, I lost 3 points in the midterm because I couldn't see the blackboard and my social anxiety kicked so hard that I couldn't ask for what was written there.
12) If the exam is a QCM, you can find inductions by working from one end and the other, and try to bridge the gap in between (easier and faster).

So that's all for me, my name is Thösam, at the time of writing this, it is Christmas (25/12/2021), I am a student at the EPFL and I hope you will grow through this journey :D.

### Last but not least :
Exercises I found particularly harder :
Ex set 8, set 11
Ex set 7 Q2
Exam 2019 Q2, Q3/d
Exam 2020 Q23, 24, 25

(idk but for yield to map, flatmap etc -> always flatmap exept the last which is always a map)
