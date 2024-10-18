# Estados

#### Diagramas del estados durante el la ejecución del flujo

El siguiente diagrama representa como viajan los estados de un intent durante un flujo exitoso&#x20;

```mermaid
sequenceDiagram
participant s as Banco Orgien
participant l as ach.minka.io
participant t as Banco Destino 

s ->> l:POST /intents <br> status: Created
l ->> s: POST /debits  <br> status: Pending
s -->> l: POST /intents/[id]/proofs<br> status: Prepared
l ->> t: POST /credits <br> status: Pending
t -->> l: POST /credits/[id]/proofs <br> status:Prepared

par Credit Commit
  l ->> t: POST /credits/[id]/commit <br> status: Committed
  t -->> l: POST /credits/[id]/proofs <br> status: Committed
and Debit Commit
  l ->> s: POST /debits/[id]/commit <br> status: Committed
  s -->> l: POST /intents/[id]/proofs <br> status: Committed
end

par Complete Credit
    l -->> s: PUT /intents/[id] <br> status: Completed
and Complete Debit
    l -->> t: PUT /intents/[id] <br> status: Completed
end
```

El siguiente diagrama representa como viajan los estados de un intent durante la fase de preparación con el debito fallido&#x20;



```
```



```mermaid
sequenceDiagram
  participant s as Banco Orgien
  participant l as ach.minka.io
  participant t as Banco Destino 
  
  s ->> l:POST /intents <br> status: Created
  l ->> s: POST /debits  <br> status: Pending
  s -->> l: POST /intents/[id]/proofs<br> status: Failed
  l ->> s: POST /debits/[id]/abort  <br> status: Aborted
  s -->> l: POST /intents/[id]/proofs<br> status: Aborted
  l -->> s: PUT /intents/[id] <br> status: Rejected
  	
```



El siguiente diagrama representa como viajan los estados de un intent durante la fase de preparación con el crédito fallido&#x20;

```mermaid
sequenceDiagram
  participant s as Banco Orgien
  participant l as ach.minka.io
  participant t as Banco Destino 
  
  s ->> l:POST /intents <br> status: Created
  l ->> s: POST /debits  <br> status: Pending
  s -->> l: POST /intents/[id]/proofs<br> status: Prepared
  l ->> t: POST /credit  <br> status: Pending
  t -->> l: POST /intents/[id]/proofs<br> status: Failed
  
  par Credit Abort
    l ->> t: POST /credit/[id]/abort  <br> status: Aborted
    t -->> l: POST /intents/[id]/proofs<br> status: Aborted
  and Debit Abort
    l ->> s: POST /debits/[id]/abort  <br> status: Aborted
    s -->> l: POST /intents/[id]/proofs<br> status: Aborted
  end
  
  par Reject Credit
    l -->> t: PUT /intents/[id] <br> status: Rejected
  and Reject Debit
    l -->> s: PUT /intents/[id] <br> status: Rejected	  	
  end  	
```



#### Diagrama de flujo de estados&#x20;

```mermaid
---
title: Intent status flow
---
stateDiagram-v2
    [*] --> Created
    Created --> Pending
    Pending --> Prepared
    Prepared --> Committed
    Committed --> Completed
    Completed --> [*]
        
    Pending --> Aborted
	  Aborted --> Rejected
    Rejected --> [*]


```



