- All code should compile without error
- Code should not produce any warnings with cargo clippy
- On structs:
    - Structs should always be created by a new() (or similarly named) method
    - Struct fields should not be public; any access to a struct’s fields should be done through public methods named after the field in an impl block
    - Here is an example of a struct I might create
    ```
    pub struct A {
    b: String,
    c: Arc<[u64]>,
    d: Option<bool>
    }


    impl A {
        pub fn new(b: String, c: Arc<[u64]>, d: Option<bool>) -> Self {
            A {b, c, d}
        }

        pub fn b(&self) -> &str {
            &self.b
        }

        pub fn c(&self) -> Arc<[u64]> {
            Arc::clone(&self.c)
        }

        pub fn d(&self) -> Option<&bool> {
            self.d.as_ref()
        }
    }
    ```
- Prefer Arc<[T]> to Vec<[T]> for collections of data
- Prefer Option<&T> to &Option<T>
- Prefer scoped threads from the standard library over tokio, where possible
