# kmeans-wasm

kmeans-wasm is adapted from [Markus Ebner's kmeans library](https://crates.io/crates/kmeans) to add wasm compatibility.
Here is a small example, using kmean++ as initialization method and lloyd as k-means variant:

```rust
use kmeans::*;

fn main() {
    let (sample_cnt, sample_dims, k, max_iter) = (20000, 200, 4, 100);

    // Generate some random data
    let mut samples = vec![0.0f64;sample_cnt * sample_dims];
    samples.iter_mut().for_each(|v| *v = rand::random());

    // Calculate kmeans, using kmean++ as initialization-method
    let kmean = KMeans::new(samples, sample_cnt, sample_dims);
    let result = kmean.kmeans_lloyd(k, max_iter, KMeans::init_kmeanplusplus, &KMeansConfig::default());

    println!("Centroids: {:?}", result.centroids);
    println!("Cluster-Assignments: {:?}", result.assignments);
    println!("Error: {}", result.distsum);
}
```


## Datastructures
For performance-reasons, all calculations are done on bare vectors, using hand-written SIMD intrinsics from the `packed_simd` crate. All vectors are stored row-major, so each sample is stored in a consecutive block of memory.

## Supported variants / algorithms
- lloyd (standard kmeans)
- minibatch

## Supported centroid initialization methods
- KMean++
- random partition
- random sample

## TODO
- Swap all calls to std to no-std-compat
- Swap rayon to wasm-bindgen-rayon
