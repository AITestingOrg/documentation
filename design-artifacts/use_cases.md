| Name                                    |  Steps |
|----------------------------------------|---------------------------------------------------------------------------------|
|Get Trip Estimate                       |  Given I am logged in as a user<br>When I choose an origin and a destination<br>Then I should be presented with a trip cost estimate|
|End Trip for User                       |  Given I am logged in as a user<br>When the driver drops me off<br>I should be presented with an invoice for payment|
|Rate for User                           |  Given I am logged in as a user<br>and I am on an invoice page<br>When I enter text in the review box and press the rate button<br>Then I am presented a thank you screen.|
|Enable Driver                           |  Given I am logged in as a Driver<br>When I click I'm available<br>Then users should be able to click me from available Driver list|
|Finish trip earlier for User OR Driver  |  Given I am logged in as a user<br>and I am on a started trip page<br>When I select to finish the trip before reaching the destination<br>Then I am presented with a confirmation page<br>When I confirm<br>Then I am presented with an invoice of payment|
|Cancel trip for User                    |  Given I am logged in as a user<br>and I am on started trip but the driver hasn't picked me up yet<br>When I select to finish the trip before the driver picks me up<br>Then I am presented with a confirmation page<br>When I confirm<br>Then I am presented with an invoice of payment with a penalty|
|Pick Up User                            |  Given I am logged in as a Driver<br>When I press the "Passenger Picked Up" button<br>Then it indicates that the trip has started.|
|Drop Off User                           |  Given I am logged in as a Driver<br>and I delivered the user to his/her final destination<br>When I select to Finish Trip<br>Then I am presented with a confirmation page and an option to be set as avaliable|
|Rating User                             |  Given I am logged in as a Driver<br>and I have dropped of the user<br>When I rate the Driver<br>Then I am presented with a confirmation page|
|Payment to Driver                       |  Given I am logged in as a user who has just finished a trip<br>When trip is ended<br>Use the user's preferred payment method for payment transaction|
|Confirmation of Payment to Driver       |  Given I am logged in as a Driver<br> and the user submitted the payment of a finished trip<br>When the application is running<br>Then I am presented with a payment confirmation message|
|Register User                           | |
|Accept Trip                             |  Given I am logged in as a user and am on a trip estimate screen<br>When I accept <br>Then the closest driver is selected and notified for pickup. My screen changes to a waiting for pickup screen|
