{% from book.macros_path import external_link %}

# Concurrency

## Immediate Contexts

When an expression is guaranteed to complete in the current cycle of our program we can use the immediate context. By default functions are immediate. If a single expression inside of a function is async then it will no longer be immediate.

## Async Contexts

When an expression may or may not complete in the current cycle of our program we must use the async context. We tell the compiler our function is async by using the `<suspends>` keyword. If a single expression inside of a function is async then the whole function is treated as async.

In our verse devices we see the async context within the OnBegin:

```ruby
OnBegin<override>()<suspends> : void =
    ThisOperationMayTakeAwhile()

ThisOperationMayTakeAwhile()<suspends> : void =
    Sleep(1.0) # waits one second
    Boss.TauntEmote() # waits until taunt emote is complete
```

## Sync

When we want to run multiple async functions at once and wait for all of them to complete before continuing we can use sync.

```ruby
BeginRace()<suspends> : void =
    Results = sync:
        Player1StartRace()
        Player2StartRace()
        Player3StartRace()
    # executes after all three above functions have been completed.
    Print("All players have completed the race!");
```

## Race

race allows us to run two or more async expressions in parallel and return the result of the first one that completes.

```ruby
StartEscapeRoomRace<suspends> : void =
    set WinnerResult = race:
        Player1BeginEscapeRoom()
        Player2BeginEscapeRoom()
        Player3BeginEscapeRoom()
    Print("{WinnerResult} has won!") # the remaining two expressions are canceled
    EndGameDevice.Activate(WinnerResult)
```

Another reason to use a race would be when you have a long running task that will never complete, paired with a task that will complete.

```ruby
StartGame<suspends> : void =
    race:
        StartGameTimer()
        EndlessBackgroundEvent() # this will never complete
```

## Rush

Rush is similar to Race, in that the program continues after the fastest expression is completed. However the slower expressions will not be canceled.

```ruby
StartGame<suspends> : void =
    race:
        FastExpression()
        SlowerExpression() # this will not be canceled

    Print("FastExpression has just completed")
```

## Branch

What if we want to execute async functions and then forget about them? In this case we can use brach, to trigger async functions and them immediately execute the next line of code following the branch.

```ruby
# Branch must be called within an async context so suspends is required
StartGame<suspends> : void =
    branch:
        BackgroundProcess()
        FireAndForgetThis()
    # print will be executed immediately
    Print("This print will be run immediately")
```

## Spawn

Spawn is similar to branch in that it triggers async functions and then immediately executes the next line of code following the spawn, but there are some important differences. Spawn can run outside of an async context, however it's limited to a single function call.

```ruby
# Branch must be called within an async context so suspends is required
StartGame : void =
    spawn:
        BackgroundProcess() # can only call a single function
    # print will be executed immediately
    Print("This print will be run immediately")
```

Epic recommends using branch over spawn whenever possible, you should only use spawn when you need to trigger an async command within an immediate context.

## Tasks

- A task is an object that represents the state of an async function that has been executed but not yet completed.

```ruby
Task2 := spawn{Player.MoveTo(Target1)}

Sleep(1.5)
MyLog.Print("1.5 Seconds into Move_to()")

Task2.Await() # wait until MoveTo() completed
```
