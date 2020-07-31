# Convert a Hard Coded String using react-intl FormattedMessage

**[📹 Video](https://egghead.io/lessons/react-convert-a-hard-coded-string-using-react-intl-formattedmessage)**

## Adding Messages for each Language ⚡
In **src/messages.js**, we'll add some messages for each supported language.

### messages.js
```js
export default {
  'en-US': {
    detail: {
      toggle: 'Toggle',
      purchase: 'Purchase this book from:',
      reviewsHeading: 'Reviews'
    }
  },
  'es': {
    detail: {
      toggle: 'Palanca',
      purchase: 'Compre este libro de:',
      reviewsHeading: 'Comentarios'
    }
  },
  'fr': {
    detail: {
      toggle:'Basculer',
      purchase: 'Achetez ce livre à partir de:',
      reviewsHeading: 'Avis'
    }
  }
}
```

We've created nested objects under each language because it provides structure and readability, but react-intl **expects a completely flat object to be provided to its props**.

## Flattening our Messages ⚡
In order to use the nested structure, we need to use a `flattenMessages` utility. In **src**, we'll create a new file called **utils.js**, and we'll define our `flattenMessages` function inside.

### utils.js
```js
export function flattenMessages(nestedMessages, prefix = '') {
    return Object.keys(nestedMessages).reduce((messages, key) => {
      let value = nestedMessages[key];
      let prefixedKey = prefix ? `${prefix}.${key}` : key;
  
      if (typeof value === 'string') {
        messages[prefixedKey] = value;
      } else {
        Object.assign(messages, flattenMessages(value, prefixedKey));
      }
  
      return messages;
    }, {});
  }
```

The above function will take one of our nested key-value pairs, such as `toggle: 'Toggle'` within `detail`, and flatten it by producing the key-value pair `detail.toggle: 'Toggle'`. This allows us to use standard JSON notation with nested key-value pairs.

We now need to import our `flattenMessages` utility into our entry-point, **src/index.js**, and use it in our `messages` prop in order for react-intl to read `messages[locale]` as a flat object.

### index.js
```js
...

ReactDOM.render(
  <IntlProvider locale={locale} messages={flattenMessages(messages[locale])}>
    <App />
  </IntlProvider>,
  document.getElementById('root')
);
```

We can begin to translate our app now that our `messages` can be read by react-intl.

## Necessary Components ⚡
To continue following along with the lesson, we need to create some  specific components.

Create a folder in **src** called **components**, and create three files:
- **BookDetail.js**
- **FeaturedBook.js**
- **Home.js**

Within each file, add the corresponding code given below:
### BookDetail.js
```js
import React from 'react';
import {meanBy, round, sortBy} from 'lodash';

import books from '../books.json';

const BookDetail = ({match}) => {
  const book = books.find(book => book.id === parseInt(match.params.bookId, 10));
  const sortedReviews = sortBy(book.reviews, 'date').reverse();

  return (
    <div className="BookDetail">
      <div className="BookDetail-meta">
        <img src={book.image} width="200" height="275" alt={book.title}/>
        <div className="BookDetail-metaBody">
          <h1>{book.title}</h1>
          <h3>by {book.author}</h3>
          <div>
            <input type="checkbox" id="toggle" hidden/>
            <p>{book.description}</p>
            <label className="BookDetail-descriptionToggle" htmlFor="toggle">Read More</label>
          </div>
        </div>
      </div>

      <h3 className="BookDetail-merchantHeading">Purchase this book from:</h3>
      <div className="BookDetail-merchants">
        {book.merchants.map((merchant) => (
          <a href={merchant.link} className="Merchant" key={merchant.name}>
            <img src={merchant.icon} width="32" height="32" alt={merchant.name}/>
            <strong>{merchant.name}</strong>
            <p>{merchant.price}</p>
          </a>
        ))}
      </div>

      <h2>Reviews</h2>
      <h3>Average Rating: {round(meanBy(book.reviews, (r) => r.rating), 2)} ({book.reviews.length} Reviews)</h3>
      <div className="BookDetail-reviews">
        {sortedReviews.map((review) => (
          <div className="Review" key={review.date}>
            <div className="Review-meta">
              <img src={review.avatar} alt="Avatar"/>
              <p>
                <strong>{review.name}</strong> rated it: {review.rating} out of 5 <br />
                {new Date(review.date).toLocaleDateString()}
              </p>
            </div>
            <p>{review.body}</p>
          </div>
        ))}
      </div>
    </div>
  )
}

export default BookDetail;
```

### FeaturedBook.js
```js
import React from 'react';
import {Link} from 'react-router-dom';

const FeaturedBook = ({book}) => {
  return (
    <Link to={`book/${book.id}`} className="FeaturedBook">
      <img src={book.image} width="200" height="275" alt={book.title}/>
      <h3>{book.title}</h3>
      <h4>{book.author}</h4>
    </Link>
  )
}

export default FeaturedBook;
```

### Home.js
```js
import React from "react";
import FeaturedBook from "./FeaturedBook";

import books from "../books.json";

const Home = () => {
  return (
    <div>
      <div className="FeaturedBooks">
        {books.map((book, i) => <FeaturedBook book={book} key={i}/>)}
      </div>
    </div>
  )
}

export default Home;
```

### Other Files to Add/Edit ⚡
In order for our project to look as it does in the lesson, we need to add some other files.

Firstly, download the image below into your **src** file.
![books.svg](./images/02-convert-a-hard-coded-string-using-react-intl-formatted-message-books.svg)

Next, edit **src/App.css** to be as follows:
### App.css
```css
* {
  box-sizing: border-box;
}

.App-header .Inner {
  display: flex;
  border-bottom: 1px solid;
}

  .App-header .Logo {
    display: flex;
    align-items: center;
    color: black;
    text-decoration: none;
  }

  .App-header img {
    height: 40px;
  }

  .App-header h2 {
    padding-left: 2rem;
    font-size: 2rem;
    white-space: nowrap;
  }


.FeaturedBooks {
  display: flex;
  flex-wrap: wrap;
}

.FeaturedBook {
  flex: 33%;
  margin-top: 3rem;
  text-align: center;
}
  .FeaturedBook:hover {
    color: darkslategray;
  }

  .FeaturedBook h3 {
    margin: 0.5rem 0;
    font-size: 1.4rem;
  }

  .FeaturedBook h4 {
    margin-top: 0;
    font-size: 1rem;
    font-weight: normal;
  }

.BookDetail-meta {
  display: flex;
  margin-top: 2rem;
}

  .BookDetail-meta img {
    flex: 0 0 200px;
  }

  .BookDetail-metaBody {
    margin-left: 2rem;
  }

  .BookDetail-meta h1 {
    margin-top: 0;
  }

  .BookDetail-meta p {
    white-space: pre-wrap;
    max-height: 130px;
    overflow-y: hidden;
  }

  .BookDetail-meta input:checked ~ p {
    max-height: none;
  }

  .BookDetail-descriptionToggle {
    all: unset;
    cursor: pointer;
    color: indianred;
  }

.BookDetail-merchantHeading {
  margin: 3rem 0 0;
}

.BookDetail-merchants {
  display: flex;
  justify-content: space-between;
  max-width: 400px;
}

  .Merchant {
    margin-top: 2rem;
    text-align: center;
  }

    .Merchant img {
      display: block;
      margin: 0 auto 1rem;
      border-radius: 50%;
    }

.BookDetail-reviews {
  display: flex;
  flex-wrap: wrap;
  margin-top: 3rem;
}

  .Review {
    padding: 0 1rem 1rem;
    margin-bottom: 2rem;
  }

    .Review:not(:last-child) {
      border-bottom: 1px solid #cfcfcf;
    }

  .Review-meta {
    display: flex;
    align-items: center;
  }
    .Review-meta img {
      flex: 0 0 64px;
      margin-right: 1rem;
      border-radius: 50%;
    }

    .Review-meta p {
      line-height: 1.6;
    }

@media screen and (min-width: 800px) {
  .Review {
    flex: 0 1 45%;
    padding: 0 1rem 1rem;
    margin-bottom: 2rem;
    border-bottom: 0 !important;
  }

    .Review:nth-child(odd) {
      padding-left: 0;
    }

    .Review:nth-child(even) {
      margin-left: auto;
      padding-right: 0;
    }
}
```
With these changes, our project repository should now be caught up with the course.

## Displaying our Messages with FormattedMessage ⚡
Start your development server in the terminal,
```bash
yarn start
```
And navigate to `localhost:3000` to see our project. If there are no errors, we should see the following:

![Homepage](./images\02-convert-a-hard-coded-string-using-react-intl-formatted-message-homepage.png)

Click on one of the books to view information about that book.

![Book Information](./images/02-convert-a-hard-coded-string-using-react-intl-formatted-message-book-information.png)

Our goal now is to internationalize the "Read More" text by having it display from our `messages`.

To do this, we'll first navigate to **src/components/BookDetail.js**, and import `FormattedMessage` from react-intl.

Next, we'll navigate to our "Read More" text in the component and replace it with a `<FormattedMessage />` component. In this component, we'll pass in an `id` prop that directly correlates to the message we want to use within `messages.js`. In this case we'll assign `detail.toggle` to `id`.

### BookDetail.js
```js
import React from 'react';
// New Import
import { FormattedMessage } from 'react-intl'
import {meanBy, round, sortBy} from 'lodash';

import books from '../books.json';

...

<label className="BookDetail-descriptionToggle" htmlFor="toggle">
    <FormattedMessage id="detail.toggle" />
</label>
...
```
Saving and returning to our development server, we should see our change in place:

![Toggle Button](./images/02-convert-a-hard-coded-string-using-react-intl-formatted-message-toggle-button.png)

We'll now also replace the "Purchase this book from" and "Reviews" headers with `FormattedMessage` components. The former will display `detail.purchase` and the latter will display `detail.reviewsHeading`.

### BookDetail.js
```js
...

<h3 className="BookDetail-merchantHeading">
    <FormattedMessage id="detail.purchase"/>
</h3>
...

<h2>
    <FormattedMessage id="detail.reviewsHeading"/>
</h2>
...
```

When we save our changes, we can view our webpage in different languages to see our translated messages displayed.

### English
![English Translation](./images/02-convert-a-hard-coded-string-using-react-intl-formatted-message-english-translation.png)

### Spanish
![Spanish Translation](images/02-convert-a-hard-coded-string-using-react-intl-formatted-message-spanish-translation.png)

### French
![French Translation](./images/02-convert-a-hard-coded-string-using-react-intl-formatted-message-french-translation.png)

## Resources 📖
- [FormattedMessage - react-intl](https://formatjs.io/docs/react-intl/components/#formattedmessage)