| Name,Steps                                                                                                     | 
|----------------------------------------------------------------------------------------------------------------| 
| ,                                                                                                              | 
| Get Trip Estimate,Given I am logged in as a user                                                               | 
| ,When I choose a destination                                                                                   | 
| ,Then I should be presented with a trip cost estimate                                                          | 
| ,                                                                                                              | 
| End Trip for User,Given I am logged in as a user                                                               | 
| ,When the driver drops me off                                                                                  | 
| ,I should be presented with an invoice for payment                                                             | 
| ,                                                                                                              | 
| Rate for User,Given I am logged in as a user                                                                   | 
| ,and I am on an invoice page                                                                                   | 
| ,When I enter text in the review box and press the rate button                                                 | 
| ,Then I am presented a thank you screen.                                                                       | 
| ,                                                                                                              | 
| Enable Driver,Given I am logged in as a Driver                                                                 | 
| ,When I click I'm available                                                                                    | 
| ,Then users should be able to click me from available Driver list                                              | 
| ,                                                                                                              | 
| Finish trip earlier for User OR Driver,Given I am logged in as a user                                          | 
| ,and I am on a started trip page                                                                               | 
| ,When I select to finish the trip before reaching the destination                                              | 
| ,Then I am presented with a confirmation page                                                                  | 
| ,When I confirm                                                                                                | 
| ,Then I am presented with an invoice of payment                                                                | 
| ,                                                                                                              | 
| Cancel trip for User,Given I am logged in as a user                                                            | 
| ,and I am on started trip but the driver hasn't picked me up yet                                               | 
| ,When I select to finish the trip before the driver picks me up                                                | 
| ,Then I am presented with a confirmation page                                                                  | 
| ,When I confirm                                                                                                | 
| ,Then I am presented with an invoice of payment with a penalty                                                 | 
| ,                                                                                                              | 
| Pick Up User,Given I am logged in as a Driver                                                                  | 
| ,When I press the "Passenger Picked Up" button                                                                 | 
| ,Then it indicates that the trip has started.                                                                  | 
| ,                                                                                                              | 
| Drop Off User,Given I am logged in as a Driver                                                                 | 
| ,and I delivered the user to his/her final destination                                                         | 
| ,When I select to Finish Trip                                                                                  | 
| ,Then I am presented with a confirmation page and an option to be set as avaliable                             | 
| ,                                                                                                              | 
| Rating User,Given I am logged in as a Driver                                                                   | 
| ,and I have dropped of the user                                                                                | 
| ,When I rate the Driver                                                                                        | 
| ,Then I am presented with a confirmation page                                                                  | 
| ,                                                                                                              | 
| Payment to Driver,Given I am logged in as a user who has just finished a trip                                  | 
| ,When trip is ended                                                                                            | 
| ,Use the user's preferred payment method for payment transaction                                               | 
| ,                                                                                                              | 
| Confirmation of Payment to Driver,Given I am logged in as a Driver                                             | 
| ,and the user submitted the payment of a finished trip                                                         | 
| ,When the application is running                                                                               | 
| ,Then I am presented with a payment confirmation message                                                       | 
| ,                                                                                                              | 
| Register User,                                                                                                 | 
| ,                                                                                                              | 
| ,                                                                                                              | 
| ,                                                                                                              | 
| Accept Trip,Given I am logged in as a user and am on a trip estimate screen                                    | 
| ,When I accept the trip                                                                                        | 
| ,Then the closest driver is selected and notified for pickup. My screen changes to a waiting for pickup screen | 
