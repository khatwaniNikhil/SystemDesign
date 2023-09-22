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

# JSON based Application level Class Design
public enum ExpenseType {
    EQUAL, PERCENT, FIXED    
}

@JsonTypeInfo(use = JsonTypeInfo.Id.NAME, property = "type")
@JsonSubTypes({
    @JsonSubTypes.Type(value=EqualExpense.class, name="EQUAL"),
    @JsonSubTypes.Type(value=PercentExpense.class, name="PERCENT")
})

public  class AbstractExpense {
    private final String code;
    private final Person paidBy;
    private final BigDecimal paidAmount;
    private final ExpenseType type;
    private final Set<Person> personsSharingExpense;

    
    public Expense(String code, Person paidBy, BigDecimal paidAmount, SplitType splitType, Set<Person> personsSharingExpense) {
        this.code = code;
        this.paidBy = paidBy;
        this.paidAmount = paidAmount;
        this.splitType  = splitType;
        this.personsSharingExpense = personsSharingExpense;
        calculateTransfers();
    }
}

public class EqualExpense extends AbstractExpense  {
    ...
}

public class PercentExpense extends AbstractExpense {
    ...
}
