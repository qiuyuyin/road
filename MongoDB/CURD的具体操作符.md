### Fields（基础类型）

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$currentDate`](https://docs.mongodb.com/v4.4/reference/operator/update/currentDate/#mongodb-update-up.-currentDate) | Sets the value of a field to current date, either as a Date or a Timestamp. |
| [`$inc`](https://docs.mongodb.com/v4.4/reference/operator/update/inc/#mongodb-update-up.-inc) | Increments the value of the field by the specified amount.   |
| [`$min`](https://docs.mongodb.com/v4.4/reference/operator/update/min/#mongodb-update-up.-min) | Only updates the field if the specified value is less than the existing field value. |
| [`$max`](https://docs.mongodb.com/v4.4/reference/operator/update/max/#mongodb-update-up.-max) | Only updates the field if the specified value is greater than the existing field value. |
| [`$mul`](https://docs.mongodb.com/v4.4/reference/operator/update/mul/#mongodb-update-up.-mul) | Multiplies the value of the field by the specified amount.   |
| [`$rename`](https://docs.mongodb.com/v4.4/reference/operator/update/rename/#mongodb-update-up.-rename) | Renames a field.                                             |
| [`$set`](https://docs.mongodb.com/v4.4/reference/operator/update/set/#mongodb-update-up.-set) | Sets the value of a field in a document.                     |
| [`$setOnInsert`](https://docs.mongodb.com/v4.4/reference/operator/update/setOnInsert/#mongodb-update-up.-setOnInsert) | Sets the value of a field if an update results in an insert of a document. Has no effect on update operations that modify existing documents. |
| [`$unset`](https://docs.mongodb.com/v4.4/reference/operator/update/unset/#mongodb-update-up.-unset) | Removes the specified field from a document.                 |

### Array（数组类型）

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$`](https://docs.mongodb.com/v4.4/reference/operator/update/positional/#mongodb-update-up.-) | Acts as a placeholder to update the first element that matches the query condition. |
| [`$[\]`](https://docs.mongodb.com/v4.4/reference/operator/update/positional-all/#mongodb-update-up.---) | Acts as a placeholder to update all elements in an array for the documents that match the query condition. |
| [`$[\]`](https://docs.mongodb.com/v4.4/reference/operator/update/positional-filtered/#mongodb-update-up.---identifier--) | Acts as a placeholder to update all elements that match the `arrayFilters` condition for the documents that match the query condition. |
| [`$addToSet`](https://docs.mongodb.com/v4.4/reference/operator/update/addToSet/#mongodb-update-up.-addToSet) | Adds elements to an array only if they do not already exist in the set. |
| [`$pop`](https://docs.mongodb.com/v4.4/reference/operator/update/pop/#mongodb-update-up.-pop) | Removes the first or last item of an array.                  |
| [`$pull`](https://docs.mongodb.com/v4.4/reference/operator/update/pull/#mongodb-update-up.-pull) | Removes all array elements that match a specified query.     |
| [`$push`](https://docs.mongodb.com/v4.4/reference/operator/update/push/#mongodb-update-up.-push) | Adds an item to an array.                                    |
| [`$pullAll`](https://docs.mongodb.com/v4.4/reference/operator/update/pullAll/#mongodb-update-up.-pullAll) | Removes all matching values from an array.                   |

#### Modifiers

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$each`](https://docs.mongodb.com/v4.4/reference/operator/update/each/#mongodb-update-up.-each) | Modifies the [`$push`](https://docs.mongodb.com/v4.4/reference/operator/update/push/#mongodb-update-up.-push) and [`$addToSet`](https://docs.mongodb.com/v4.4/reference/operator/update/addToSet/#mongodb-update-up.-addToSet) operators to append multiple items for array updates. |
| [`$position`](https://docs.mongodb.com/v4.4/reference/operator/update/position/#mongodb-update-up.-position) | Modifies the [`$push`](https://docs.mongodb.com/v4.4/reference/operator/update/push/#mongodb-update-up.-push) operator to specify the position in the array to add elements. |
| [`$slice`](https://docs.mongodb.com/v4.4/reference/operator/update/slice/#mongodb-update-up.-slice) | Modifies the [`$push`](https://docs.mongodb.com/v4.4/reference/operator/update/push/#mongodb-update-up.-push) operator to limit the size of updated arrays. |
| [`$sort`](https://docs.mongodb.com/v4.4/reference/operator/update/sort/#mongodb-update-up.-sort) | Modifies the [`$push`](https://docs.mongodb.com/v4.4/reference/operator/update/push/#mongodb-update-up.-push) operator to reorder documents stored in an array. |

### Bitwise

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$bit`](https://docs.mongodb.com/v4.4/reference/operator/update/bit/#mongodb-update-up.-bit) | Performs bitwise `AND`, `OR`, and `XOR` updates of integer values. |

