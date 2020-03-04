---
date: '2020-01-08T09:59:25Z'
menu:
- corda-enterprise-4-4
title: Updating the flow
---



# Updating the flow

We now need to update our flow to achieve three things:


* Verifying that the transaction proposal we build fulfills the `IOUContract` constraints


* Updating the lender’s side of the flow to request the borrower’s signature


* Creating a response flow for the borrower that responds to the signature request from the lender


We’ll do this by modifying the flow we wrote in the previous tutorial.


## Verifying the transaction

In `IOUFlow.java`/`Flows.kt`, change the imports block to the following:


{{< tabs name="tabs-1" >}}

{{< /tabs >}}

And update `IOUFlow.call` to the following:


{{< tabs name="tabs-2" >}}

{{< /tabs >}}

In the original CorDapp, we automated the process of notarising a transaction and recording it in every party’s vault
                by invoking a built-in flow called `FinalityFlow` as a subflow. We’re going to use another pre-defined flow,
                `CollectSignaturesFlow`, to gather the borrower’s signature.

First, we need to update the command. We are now using `IOUContract.Create`, rather than
                `TemplateContract.Commands.Action`. We also want to make the borrower a required signer, as per the contract
                constraints. This is as simple as adding the borrower’s public key to the transaction’s command.

We also need to add the output state to the transaction using a reference to the `IOUContract`, instead of to the old
                `TemplateContract`.

Now that our state is governed by a real contract, we’ll want to check that our transaction proposal satisfies these
                requirements before kicking off the signing process. We do this by calling `TransactionBuilder.verify` on our
                transaction proposal before finalising it by adding our signature.


## Requesting the borrower’s signature

Previously we wrote a responder flow for the borrower in order to receive the finalised transaction from the lender.
                We use this same flow to first request their signature over the transaction.

We gather the borrower’s signature using `CollectSignaturesFlow`, which takes:


* A transaction signed by the flow initiator


* A list of flow-sessions between the flow initiator and the required signers


And returns a transaction signed by all the required signers.

We can then pass this fully-signed transaction into `FinalityFlow`.


## Updating the borrower’s flow

On the lender’s side, we used `CollectSignaturesFlow` to automate the collection of signatures. To allow the borrower
                to respond, we need to update its responder flow to first receive the partially signed transaction for signing. Update
                `IOUFlowResponder.call` to be the following:


{{< tabs name="tabs-3" >}}

{{< /tabs >}}

We could write our own flow to handle this process. However, there is also a pre-defined flow called
                `SignTransactionFlow` that can handle the process automatically. The only catch is that `SignTransactionFlow` is an
                abstract class - we must subclass it and override `SignTransactionFlow.checkTransaction`.


### CheckTransactions

`SignTransactionFlow` will automatically verify the transaction and its signatures before signing it. However, just
                    because a transaction is contractually valid doesn’t mean we necessarily want to sign. What if we don’t want to deal
                    with the counterparty in question, or the value is too high, or we’re not happy with the transaction’s structure?

Overriding `SignTransactionFlow.checkTransaction` allows us to define these additional checks. In our case, we are
                    checking that:


* The transaction involves an `IOUState` - this ensures that `IOUContract` will be run to verify the transaction


* The IOU’s value is less than some amount (100 in this case)


If either of these conditions are not met, we will not sign the transaction - even if the transaction and its
                    signatures are contractually valid.

Once we’ve defined the `SignTransactionFlow` subclass, we invoke it using `FlowLogic.subFlow`, and the
                    communication with the borrower’s and the lender’s flow is conducted automatically.

`SignedTransactionFlow` returns the newly signed transaction. We pass in the transaction’s ID to `ReceiveFinalityFlow`
                    to ensure we are recording the correct notarised transaction from the lender.


## Conclusion

We have now updated our flow to verify the transaction and gather the lender’s signature, in line with the constraints
                defined in `IOUContract`. We can now re-run our updated CorDapp, using the
                [same instructions as before](hello-world-running.md).

Our CorDapp now imposes restrictions on the issuance of IOUs. Most importantly, IOU issuance now requires agreement
                from both the lender and the borrower before an IOU can be created on the blockchain. This prevents either the lender or
                the borrower from unilaterally updating the ledger in a way that only benefits themselves.

After completing this tutorial, your CorDapp should look like this:


* Java: [https://github.com/corda/corda-tut2-solution-java](https://github.com/corda/corda-tut2-solution-java)


* Kotlin: [https://github.com/corda/corda-tut2-solution-kotlin](https://github.com/corda/corda-tut2-solution-kotlin)


You should now be ready to develop your own CorDapps. You can also find a list of sample CorDapps
                [here](https://www.corda.net/samples/). You are now ready to learn more about the Corda API.

If you get stuck at any point, please reach out on [Slack](https://slack.corda.net/) or
                [Stack Overflow](https://stackoverflow.com/questions/tagged/corda).

