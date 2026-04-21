# D2 sample diagrams

The following diagrams are valid examples and should be used as a reference before elaborating any new D2 diagram:

## System architecture example 01

```d2
bank:   {
  style.fill: white
  Corporate:   {
    style.fill: white
    app14506: Data Source\ntco:      100,000\nowner: Lakshmi  {
      style:  {
        fill: '#fce7c6'
      }
    }
  }
  Equities:   {
    app14491: Risk Global\ntco:      600,000\nowner: Wendy  {
      style:  {
        fill: '#f6c889'
      }
    }
    app14492: Credit guard\ntco:      100,000\nowner: Lakshmi  {
      style:  {
        fill: '#fce7c6'
      }
    }
    app14520: Seven heaven\ntco:      100,000\nowner: Tomos  {
      style:  {
        fill: '#fce7c6'
      }
    }
    app14522: Apac Ace\ntco:      400,000\nowner: Wendy  {
      style:  {
        fill: '#f9d8a7'
      }
    }
    app14527: Risk Global\ntco:      900,000\nowner: Tomos  {
      style:  {
        fill: '#f4b76c'
      }
    }
  }
  Securities:   {
    style.fill: white
    app14517: Zone out\ntco:      500,000\nowner: Wendy  {
      style:  {
        fill: '#f6c889'
      }
    }
  }
  Finance:   {
    style.fill: white
    app14488: Credit guard\ntco:      700,000\nowner: India  {
      style:  {
        fill: '#f6c889'
      }
    }
    app14502: Ark Crypto\ntco:    1,500,000\nowner: Wendy  {
      style:  {
        fill: '#ed800c'
      }
    }
    app14510: Data Solar\ntco:    1,200,000\nowner: Deepak  {
      style:  {
        fill: '#f1a64f'
      }
    }
  }
  Risk:   {
    style.fill: white
    app14490: Seven heaven\ntco:            0\nowner: Joesph  {
      style:  {
        fill: '#fce7c6'
      }
    }
    app14507: Crypto Bot\ntco:    1,100,000\nowner: Wendy  {
      style:  {
        fill: '#f1a64f'
      }
    }
  }
  Funds:   {
    style.fill: white
    app14497: Risk Global\ntco:      500,000\nowner: Joesph  {
      style:  {
        fill: '#f6c889'
      }
    }
  }
  Fixed Income:   {
    style.fill: white
    app14523: ARC3\ntco:      600,000\nowner: Wendy  {
      style:  {
        fill: '#f6c889'
      }
    }
    app14500: Acmaze\ntco:      100,000\nowner: Tomos  {
      style:  {
        fill: '#fce7c6'
      }
    }
  }
}
bank.Risk.app14490 -> bank.Equities.app14527: client master
bank.Equities.app14491 -> bank.Equities.app14527: greeks  {
  style:  {
    stroke-dash: 5
    animated: true
    stroke: red
  }
}
bank.Funds.app14497 -> bank.Equities.app14520: allocations  {
  style:  {
    stroke-dash: 5
    animated: true
    stroke: brown
  }
}
bank.Equities.app14527 -> bank.Corporate.app14506: trades  {
  style:  {
    stroke-dash: 5
    animated: false
    stroke: blue
  }
}
bank.Fixed Income.app14523 -> bank.Equities.app14491: orders  {
  style:  {
    stroke-dash: 10
    animated: false
    stroke: green
  }
}
bank.Finance.app14488 -> bank.Equities.app14527: greeks  {
  style:  {
    stroke-dash: 5
    animated: true
    stroke: red
  }
}
bank.Equities.app14527 -> bank.Equities.app14522: orders  {
  style:  {
    stroke-dash: 10
    animated: false
    stroke: green
  }
}
bank.Equities.app14522 -> bank.Finance.app14510: orders  {
  style:  {
    stroke-dash: 10
    animated: false
    stroke: green
  }
}
bank.Equities.app14527 -> bank.Finance.app14502: greeks  {
  style:  {
    stroke-dash: 5
    animated: true
    stroke: red
  }
}
bank.Equities.app14527 -> bank.Risk.app14507: allocations  {
  style:  {
    stroke-dash: 5
    animated: true
    stroke: brown
  }
}
bank.Securities.app14517 -> bank.Equities.app14492: trades  {
  style:  {
    stroke-dash: 5
    animated: false
    stroke: blue
  }
}
bank.Equities.app14522 -> bank.Fixed Income.app14500: security reference
```

## System diagram example 02

```d2
direction: right

classes: {
  base: {
    style: {
      bold: true
      font-size: 28
    }
  }

  person: {
    shape: person
  }

  animated: {
    style: {
      animated: true
    }
  }

  multiple: {
    style: {
      multiple: true
    }
  }

  enqueue: {
    label: Enqueue Task
  }

  dispatch: {
    label: Dispatch Task
  }

  library: {
    style: {
      bold: true
      font-size: 32
      fill: PapayaWhip
      fill-pattern: grain
      border-radius: 8
      font: mono
    }
  }

  task: {
    style: {
      bold: true
      font-size: 32
    }
  }
}

user01: {
  label: User01
  class: [base; person; multiple]
}

user02: {
  label: User02
  class: [base; person; multiple]
}

user03: {
  label: User03
  class: [base; person; multiple]
}

user01 -> container.task01: {
  label: Create Task
  class: [base; animated]
}
user02 -> container.task02: {
  label: Create Task
  class: [base; animated]
}
user03 -> container.task03: {
  label: Create Task
  class: [base; animated]
}

container: Application {
  direction: right
  style: {
    bold: true
    font-size: 28
  }
  icon: https://icons.terrastruct.com/dev%2Fgo.svg

  task01: {
    icon: https://icons.terrastruct.com/essentials%2F092-graph%20bar.svg
    class: [task; multiple]
  }

  task02: {
    icon: https://icons.terrastruct.com/essentials%2F095-download.svg
    class: [task; multiple]
  }

  task03: {
    icon: https://icons.terrastruct.com/essentials%2F195-attachment.svg
    class: [task; multiple]
  }

  queue: {
    label: Queue Library
    icon: https://icons.terrastruct.com/dev%2Fgo.svg
    style: {
      bold: true
      font-size: 32
      fill: honeydew
    }

    producer: {
      label: Producer
      class: library
    }

    consumer: {
      label: Consumer
      class: library
    }

    database: {
      label: Ring\nBuffer
      shape: cylinder
      style: {
        bold: true
        font-size: 32
        fill-pattern: lines
        font: mono
      }
    }

    producer -> database
    database -> consumer
  }

  worker01: {
    icon: https://icons.terrastruct.com/essentials%2F092-graph%20bar.svg
    class: [task]
  }

  worker02: {
    icon: https://icons.terrastruct.com/essentials%2F095-download.svg
    class: [task]
  }

  worker03: {
    icon: https://icons.terrastruct.com/essentials%2F092-graph%20bar.svg
    class: [task]
  }

  worker04: {
    icon: https://icons.terrastruct.com/essentials%2F195-attachment.svg
    class: [task]
  }

  task01 -> queue.producer: {
    class: [base; enqueue]
  }
  task02 -> queue.producer: {
    class: [base; enqueue]
  }
  task03 -> queue.producer: {
    class: [base; enqueue]
  }
  queue.consumer -> worker01: {
    class: [base; dispatch]
  }
  queue.consumer -> worker02: {
    class: [base; dispatch]
  }
  queue.consumer -> worker03: {
    class: [base; dispatch]
  }
  queue.consumer -> worker04: {
    class: [base; dispatch]
  }
}
```

## Sequence diagram example

```d2
shape: sequence_diagram

User
Session
Lua

User."Init"

User.t1 -> Session.t1: "SetupFight()"
Session.t1 -> Session.t1: "Create clean fight state"
Session.t1 -> Lua: "Trigger OnPlayerTurn"
User.t1 <- Session.t1

User."Repeat"

User.mid -> Session.mid: "PlayerCastHand() etc."
Session.mid -> Lua: "Trigger OnDamage etc."
User.mid <- Session.mid

User.t2 -> Session.t2: "FinishPlayerTurn()"
Session.t2 -> Lua: "Trigger OnTurn"
Session.t2 -> Session.t2: "Update and remove status effects"
Session.t2 -> Lua: "Trigger OnPlayerTurn"
User.t2 <- Session.t2
```

## Cloud architecture diagram 01

```d2
direction: right

github: GitHub {
  shape: image
  icon: https://icons.terrastruct.com/dev%2Fgithub.svg
  style: {
    font-color: green
    font-size: 30
  }
}

github_actions: GitHub Actions {
  lambda_action: Lambda Action {
    icon: https://icons.terrastruct.com/dev%2Fgithub.svg
    style.multiple: true
  }
  style: {
    stroke: blue
    font-color: purple
    stroke-dash: 3
    fill: white
  }
}

aws: AWS Cloud VPC {
  style: {
    font-color: purple
    fill: white
    opacity: 0.5
  }
  lambda01: Lambda01 {
    icon: https://icons.terrastruct.com/aws%2FCompute%2FAWS-Lambda.svg
    shape: parallelogram
    style.fill: "#B6DDF6"
  }
  lambda02: Lambda02 {
    icon: https://icons.terrastruct.com/aws%2FCompute%2FAWS-Lambda.svg
    shape: parallelogram
    style.fill: "#B6DDF6"
  }
  lambda03: Lambda03 {
    icon: https://icons.terrastruct.com/aws%2FCompute%2FAWS-Lambda.svg
    shape: parallelogram
    style.fill: "#B6DDF6"
  }
}

github -> github_actions: GitHub Action Flow {
  style: {
    animated: true
    font-size: 20
  }
}
github_actions -> aws.lambda01: Update Lambda {
  style: {
    animated: true
    font-size: 20
  }
}
github_actions -> aws.lambda02: Update Lambda {
  style: {
    animated: true
    font-size: 20
  }
}
github_actions -> aws.lambda03: Update Lambda {
  style: {
    animated: true
    font-size: 20
  }
}
```
