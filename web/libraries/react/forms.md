# Forms

- preventing page reload on form submit

    ```js
    const handleFormSubmit = (e) => {
        e.preventDefault();
        e.stopPropagation();
    }
    ```

- good form libraries
    1. [Formik](https://formik.org/docs/overview)
    1. [Redux Form](https://redux-form.com/8.3.0/)
