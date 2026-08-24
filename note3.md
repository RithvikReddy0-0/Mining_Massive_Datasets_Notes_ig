## Evalution Component were discussed

- Report in AUMS (AI usage will be checked)
- Presentation
- PySpark is important and use of ML in endsems (We have a VIVA for 10 marks)

--
He have talked about how to use MapReduce, the matrix multiplication. The formula was given.

## 2nd MR Job

- It keeps pairs as they are
- Both input and output are of the form ((i,k),value)

For Rduce

- For each (i,k) it sums all the values
    ((i,k)total) --> p[i][k]

Question 1, Multiply M = [1 2,3 4]i*j , N = [5 6,7 8]j*k

Ans, SO i will convert them into triples 
For example : M:[(0,0,1),(0,1,2),(1,0,3),(1,1,4)]
              N:[(0,0,5),(0,1,6),(1,0,7),(1,1,8)]