use serde::Deserialize;
use std::fs::File;
use std::io::{self, BufReader};

#[derive(Debug, Deserialize)]
struct Matrix {
    data: Vec<Vec<f64>>,
}

fn main() -> io::Result<()> {
  
    let file = File::open("data/matriz.json")?;
    let reader = BufReader::new(file);
    let matrix: Matrix = serde_json::from_reader(reader)?;

   
    let operation: char = {
        let mut input = String::new();
        io::stdin().read_line(&mut input)?;
        input.trim().chars().next().unwrap_or(' ')
    };

  
    let (sum, count) = matrix.data.iter().enumerate().fold((0.0, 0), |acc, (i, row)| {
        let start_index = row.len() / 2 + 1;
        let values = &row[start_index..];
        let row_sum: f64 = values.iter().sum();
        (acc.0 + row_sum, acc.1 + values.len())
    });

 
    println!("Matriz com elementos da área direita destacados:");
    for row in matrix.data.iter() {
        for (j, &value) in row.iter().enumerate() {
            if j >= row.len() / 2 {
                print!("\x1b[32m{}\x1b[0m ", value); 
            } else {
                print!("{} ", value);
            }
        }
        println!();
    }

  
    let result = match operation {
        'S' => sum,
        'M' => sum / count as f64,
        _ => {
            println!("Operação incorreta. Insira 'S' para Soma ou 'M' para Média.");
            return Ok(());
        }
    };

    
    println!("Resultado: {:.1}", result);

    Ok(())
}
