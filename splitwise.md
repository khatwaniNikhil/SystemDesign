# References
1. https://github.com/PiyushRajmsit/SplitWise
2. https://www.linkedin.com/pulse/designing-splitwise-data-modelling-expense-sharing-shrey-batra/

# Entities
1. User
2. Transactions

# Query patterns driven Data modelling (keep improving via multiple iterations)
1. User can see show total amount pending to be received by him
2. User can see show total amount pending to be sent by him.

## Data Modelling iterations
### Attempt1 - Use json to capture user txn

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
#### Cons: 
1. In order to support query patterns, we need to fetch all txn's where user was involved and check if current user paid or not and then keep updating his outstanding amount.
2. Doing this in parallel for millions of users very compute intensive even though we keep all user txn's on a single shard

### Attempt2 - Division Pattern 
1. Divide the one user txn into mutliple transfers among pair of users. source_user owes destination user some amount for transaction at timestamp T
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

#### Pros:
1. filter by source_user will make it easier to calculate outstanding amount for a given user

#### Cons:
Too many records(1 record per source per destination per transaction?) Imagine 1 million transactions involving an avg of 3 users so we get -> 1M * 6 = 6 million records. (u1-u2, u1-u3, u2-u1, u2-u3, u3-u1, u1-u2)

### Attempt3: Bucketing Pattern
1. User level records.
2. Structure of record: User as source, keep appending all incoming and outgoing transfers to any other user from source user in the same document.
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
#### Cons: 
1. transactions subarray cannot be unbounded as it will lead to performance issues

### Attempt4: Bucketing pattern with time or capacity based sub buckets
Based on some time interval or no. of transactions threshold divide into separate buckets.


# Class Diagram
TODO needs to be updated as per Attempt4 data model
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/class_daigram_splitwise.jpeg)


# JSON based Application level Class Design
```java
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
```
