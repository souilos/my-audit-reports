# Fees can be set as 0, but if an Admin changes the fees it will affects lenders & renters of current rented NFTs

## Impact

* Likelihood : MEDIUM 
* Impact : MEDIUM

reNFT allows people to rent NFTs by fulfilling a Seaport order. An owner (lender), can choose to use the BASE method (earn a payout from renting his NFTs) or the PAY method (the renter receveives money by renting the NFTs).
The renter will be able to enjoy his rented NFTs, that are gonna placed in a safe (Gnosis) where he is not able to move them to another contract.

The reNFT protocol can take fees on rentals to remunerate his Admins.
These fees can be 0, and that could be a good way to promote the protocol at first. However, updating this fees would impact all all current rented NFTs.
Although only the Admins are able to change those fees this would be unfair for a lender (owner), to see the fees updated while he is renting his NFTs.
For him that would result in making less money than promised, and also a lack of trust for future potential rents (in the case of using the BASE method).
Using the PAY method, a renter would also have less money than promised and the consequences would be the same.

## Proof of Concept

In https://github.com/re-nft/smart-contracts/blob/3ddd32455a849c3c6dc3c3aad7a33a6c9b44c291/src/policies/Admin.sol#L173 an ADMIN_ADMIN can set the fees:

```
    function setFee(uint256 feeNumerator) external onlyRole("ADMIN_ADMIN") {
        ESCRW.setFee(feeNumerator);
    }
```

Then the fees are calculated in https://github.com/re-nft/smart-contracts/blob/3ddd32455a849c3c6dc3c3aad7a33a6c9b44c291/src/modules/PaymentEscrow.sol#L88:

```
    /**
     * @dev Calculates the fee based on the fee numerator set by an admin.
     *
     * @param amount Amount for which to calculate the fee.
     */
    function _calculateFee(uint256 amount) internal view returns (uint256) {
        // Uses 10,000 as a denominator for the fee.
        return (amount * fee) / 10000;
    }
```

And the function `PaymentEscrow::_settlePayment` in here https://github.com/re-nft/smart-contracts/blob/3ddd32455a849c3c6dc3c3aad7a33a6c9b44c291/src/modules/PaymentEscrow.sol#L215C14-L215C28 substracts the fees if there are for the payment:

```
                // Take a fee on the payment amount if the fee is on.
                if (fee != 0) {
                    // Calculate the new fee.
                    uint256 paymentFee = _calculateFee(paymentAmount);

                    // Adjust the payment amount by the fee.
                    paymentAmount -= paymentFee;
                }
```

## Tools Used
Manual review (vscode).

## Recommended Mitigation Steps
It would be recommended to associate all active rentals to a certain fees that once set cannot be updated while the rental is active.
This could be part of the rentalOrder and stored as a uint256.