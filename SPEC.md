## Parallel EVM Specification

### Introduction
    // TODO

### Plan
- Codebase code analysis (golang)
- Research and develop practical algorithm for filter
- Integrate algorithm to codebase

### Implementation
- Phase 1: Transaction Type filtering  
- Phase 2: Multi Stage Execution  
- Phase 3: Transaction Scheduling  
- Phase 4: Transaction Dispatcher  
    
### Scenario
example transaction that will lead to create conflict if execute in parallel.  

#### Transfer
```
### Transaction Pattern ###
form: <sender>
to: <recipient>
value: <amount>
data: null
```
- Alice transafer to Bob  
- Bob transfer to Charlie  
- Charlie transfer to Dan

|  TX | Scenario  | Conflict  |
|---|---|---|  
| 1  | Alice transafer to Bob |  TRUE |
| 2 |  Bob transfer to Charlie  |  TRUE |
| 3 | Charlie transfer to Dan  | TRUE  |

#### Transfer to Contract
```
### Transaction Pattern ###
form: <sender>
to: <contract>
value: <amount>
data: null
```
- Alice transafer to Contract_A  
- Bob transfer to Contract_B  
- Charlie transfer to Contract_C  
*** Remark `if contract implementng receive function possible to create conflict`  


|  TX | Scenario  | Conflict  |
|---|---|---|  
| 1 | Alice transafer to Contract_A |  FALSE |
| 2 | Bob transfer to Contract_B  |  TRUE |
| 3 | Charlie transfer to Contract_B  | TRUE  |

#### Calldata to Contract
```
### Transaction Schema ###
form: <sender>
to: <recipient>
value: 0
data: <datahex>
```
- Alice make a call to Contract_C  
- Bob make a call to Contract_B  
- Charlie make a call to Contract_C  
*** Remark `if Contract B have function to call Contract C will lead tx3 to conflict`


|  TX | Scenario  | Conflict  |
|---|---|---|  
| 1 | Alice make a call to Contract_C |  TRUE |
| 2 | Bob make a call to Contract_B  |  FALSE |
| 3 | Charlie make a call to Contract_C  | TRUE  |

#### Create Contract
```
### Transaction Schema ###
form: <sender>
to: null
value: 0
data: <datahex>
```
- Alice create Contract_A  
- Bob create Contract_B  
- Charlie create Contract_C  
*** Remark `if contract implementng construct possible to create conflict`

|  TX | Scenario  | Conflict  |
|---|---|---|  
| 1 | Alice create Contract_C |  FALSE |
| 2 | Bob create Contract_B  |  FALSE |
| 3 | Charlie create Contract_D  | FALSE  |


#### Create Contract with Value
```
### Transaction Schema ###
form: <sender>
to: null
value: <amount>
data: <datahex>
```
- Alice create Contract_A  
- Bob create Contract_B  
- Charlie create Contract_C  
*** Remark `if contract implementng construct possible to create conflict`

|  TX | Scenario  | Conflict  |
|---|---|---|  
| 1 | Alice create Contract_A |  FALSE |
| 2 | Bob create Contract_B  |  FALSE |
| 3 | Charlie create Contract_C  | FALSE  |

### Algorithm

implementing algorithm in ` core/state_processor.go`  
``` go
    // TODO @FilteringTransaction
    FilteringTransaction(<transaction>) (<transaction>, error) {
        // TODO adding algorithm to sort trasnaction and filter transaction that will not create conflict
        return nil
    }

    // TODO @ApplyNonConflictTransaction
    // applyTransaction in parallel way
        applyTransaction(<transaction>) (<txs_receipt>, error) {
        // TODO execute non conflict transaction in parallel
        //
        // import "os"
	    // "os/exec"
	    // "sync"
        // 
        // const numProcesses = 5
        // var wg sync.WaitGroup
        // for i := 1; i <= numProcesses; i++ {
        // 	wg.Add(1)
        // 	go executeTransaction(i, &wg)
        // }
        // wg.Wait()
        // fmt.Println("All processes have finished.")
        //
        return nil
    }
```