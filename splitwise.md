# References
1. https://github.com/PiyushRajmsit/SplitWise
2. https://www.linkedin.com/pulse/designing-splitwise-data-modelling-expense-sharing-shrey-batra/

# Class Diagram
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/class_daigram_splitwise.jpeg)


# Query patterns
1. per person - show total amount pending send/receive
      store all transactions where person has receivables  
      store all transactions where person has to send money 

      groupBY and SUM


2. per person - details - friends from whom to send_to or receive_from money
    input: P1 receives N1   P2 has to send N2   Pi send/receive Ni
    output: least no of transactions based transfer

source_user owes destination user some amount for transaction at timestamp T

************************************************
```json
{
  "id": "transaction_1",
  "paid_by": {
    "user": "user_1",
    "amount": 150,
    "distribution": "EQUAL"
  },
  "users": ["user_1", "user_2", "user_3"]
}
```
************************************************
```json
{ 
    "source": "user_1",
    "destination": "user_2",
    "timestamp": 1234,
    "amount": 50
},
{
    "source": "user_2",
    "destination": "user_1",
    "timestamp": 1234,
    "amount": -50
},
{
    "source": "user_1",
    "destination": "user_4",
    "timestamp": 1234,
    "amount": -50
}
```
******************************************************
```json
{
    "source": "user_1",
    "destination": "user_2",
    "startTime": "01 Jan 2020",
    "endTime": "31 Jan 2020",
    "trip": "trip123",
    "transactions": [
      {
          "timestamp": "t1",
          "amount": 50
      },
      {
        "timestamp": "t2",
        "amount": -50
      },
    ],
    "totalAmount": 100
}
```
