| Name                                    |  Steps |
|----------------------------------------|---------------------------------------------------------------------------------|
|Get Trip Estimate                       |  Given I am logged in as a user\s\sWhen I choose a destination\s\sThen I should be presented with a trip cost estimate|
|End Trip for User                       |  Given I am logged in as a user\s\sWhen the driver drops me off\s\sI should be presented with an invoice for payment|
|Rate for User                           |  Given I am logged in as a user\s\sand I am on an invoice page\s\sWhen I enter text in the review box and press the rate button\s\sThen I am presented a thank you screen.|
|Enable Driver                           |  Given I am logged in as a Driver\s\sWhen I click I'm available\s\sThen users should be able to click me from available Driver list|
|Finish trip earlier for User OR Driver  |  Given I am logged in as a user\s\sand I am on a started trip page\s\sWhen I select to finish the trip before reaching the destination\s\sThen I am presented with a confirmation page\s\sWhen I confirm\s\sThen I am presented with an invoice of payment|
|Cancel trip for User                    |  Given I am logged in as a user\s\sand I am on started trip but the driver hasn't picked me up yet\s\sWhen I select to finish the trip before the driver picks me up\s\sThen I am presented with a confirmation page\s\sWhen I confirm\s\sThen I am presented with an invoice of payment with a penalty|
|Pick Up User                            |  Given I am logged in as a Driver\s\sWhen I press the "Passenger Picked Up" button\s\sThen it indicates that the trip has started.|
|Drop Off User                           |  Given I am logged in as a Driver\s\sand I delivered the user to his/her final destination\s\sWhen I select to Finish Trip\s\sThen I am presented with a confirmation page and an option to be set as avaliable|
|Rating User                             |  Given I am logged in as a Driver\s\sand I have dropped of the user\s\sWhen I rate the Driver\s\sThen I am presented with a confirmation page|
|Payment to Driver                       |  Given I am logged in as a user who has just finished a trip\s\sWhen trip is ended\s\sUse the user's preferred payment method for payment transaction|
|Confirmation of Payment to Driver       |  Given I am logged in as a Driver\s\s and the user submitted the payment of a finished trip\s\sWhen the application is running\s\sThen I am presented with a payment confirmation message|
|Register User                           | |
|Accept Trip                             |  Given I am logged in as a user and am on a trip estimate screen\s\sWhen I accept \s\sThen the closest driver is selected and notified for pickup. My screen changes to a waiting for pickup screen|
