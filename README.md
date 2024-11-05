# multilabel_classification

import pandas as pd
import numpy as np
import random

def generate_airline_dataset(n_samples=1000):
    """Generate synthetic airline services dataset"""
    
    # Define basic data elements
    airlines = [
        'Emirates', 'Qatar Airways', 'Singapore Airlines', 'Air France',
        'Lufthansa', 'British Airways', 'American Airlines', 'Delta',
        'United Airlines', 'Turkish Airlines'
    ]
    
    routes = [
        'London-New York', 'Dubai-Singapore', 'Paris-Tokyo',
        'Sydney-Los Angeles', 'Frankfurt-Beijing', 'Madrid-Buenos Aires',
        'Amsterdam-Bangkok', 'Rome-San Francisco', 'Hong Kong-London',
        'Toronto-Mumbai'
    ]
    
    class_types = ['Economy', 'Premium Economy', 'Business', 'First']
    
    # Service descriptions for more realistic data generation
    service_descriptions = {
        'baggage': [
            'extra checked baggage',
            'additional carry-on',
            'sports equipment handling',
            'excess baggage allowance'
        ],
        'meal': [
            'special meal selection',
            'premium dining',
            'pre-ordered meals',
            'dietary specific meal'
        ],
        'seat_selection': [
            'extra legroom seat',
            'preferred seating',
            'advance seat selection',
            'exit row seat'
        ],
        'lounge_access': [
            'airport lounge access',
            'premium lounge entry',
            'business lounge access',
            'first class lounge'
        ],
        'priority_boarding': [
            'priority boarding',
            'early boarding',
            'fast track security',
            'premium check-in'
        ]
    }
    
    # Generate data
    data = []
    for _ in range(n_samples):
        # Basic flight info
        airline = random.choice(airlines)
        route = random.choice(routes)
        class_type = random.choice(class_types)
        
        # Determine services based on class type
        if class_type == 'First':
            # First class has higher probability of all services
            probs = [0.9, 0.9, 0.9, 1.0, 1.0]
        elif class_type == 'Business':
            probs = [0.8, 0.8, 0.8, 0.9, 0.9]
        elif class_type == 'Premium Economy':
            probs = [0.6, 0.7, 0.7, 0.3, 0.6]
        else:  # Economy
            probs = [0.3, 0.4, 0.4, 0.1, 0.2]
        
        # Generate service flags
        services = {
            'baggage': 1 if random.random() < probs[0] else 0,
            'meal': 1 if random.random() < probs[1] else 0,
            'seat_selection': 1 if random.random() < probs[2] else 0,
            'lounge_access': 1 if random.random() < probs[3] else 0,
            'priority_boarding': 1 if random.random() < probs[4] else 0
        }
        
        # Generate description
        description_parts = [f"{class_type} ticket on {airline} ({route})"]
        for service, included in services.items():
            if included:
                description_parts.append(random.choice(service_descriptions[service]))
        
        description = " with " + ", ".join(description_parts[1:]) if len(description_parts) > 1 else description_parts[0]
        
        # Create entry
        entry = {
            'airline': airline,
            'route': route,
            'class_type': class_type,
            'description': description,
            **services,
            'price': random.uniform(
                200 if class_type == 'Economy' else
                500 if class_type == 'Premium Economy' else
                2000 if class_type == 'Business' else
                5000
            ) * (1 + sum(services.values()) * 0.1)  # Price increases with more services
        }
        
        data.append(entry)
    
    # Create DataFrame
    df = pd.DataFrame(data)
    return df

# Generate and save dataset
def create_and_save_dataset(n_samples=1000, filename='airline_services_dataset.csv'):
    df = generate_airline_dataset(n_samples)
    df.to_csv(filename, index=False)
    print(f"Dataset saved to {filename}")
    return df

# Example usage
if __name__ == "__main__":
    # Generate dataset
    df = create_and_save_dataset(n_samples=1000)
    
    # Display sample and statistics
    print("\nSample of generated dataset:")
    print(df.head())
    
    print("\nDataset statistics:")
    print(df.describe())
    
    print("\nService distribution by class:")
    for class_type in df['class_type'].unique():
        class_data = df[df['class_type'] == class_type]
        print(f"\n{class_type} Class:")
        for service in ['baggage', 'meal', 'seat_selection', 'lounge_access', 'priority_boarding']:
            print(f"{service}: {class_data[service].mean():.2%}")
