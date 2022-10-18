# HKSample_read

```swift

//
//  ContentView.swift
//  HealthBodyWeight
//
//  Created by paige shin on 2022/10/18.
//

import SwiftUI
import HealthKit

struct ContentView: View {
    
    var healthStore: HealthStore = HealthStore()
    
    var body: some View {
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundColor(.accentColor)
            Text("Hello, world!")
        }
        .padding()
        .onAppear {
            
            self.healthStore.authorizeHealthKit { success, error in
                
            }
            
            let bodyMass =  HKSampleType.quantityType(forIdentifier: .bodyMass)!
            let height =  HKSampleType.quantityType(forIdentifier: .height)!
            let bodyFat = HKSampleType.quantityType(forIdentifier: .bodyFatPercentage)!
            let bodyMassIndex = HKSampleType.quantityType(forIdentifier: .bodyMassIndex)!

            
            self.healthStore.read(type: bodyMass) { result, date in
                print("BODY MASS: \(result.doubleValue(for: .gramUnit(with: .kilo)))")
            }
            
            self.healthStore.read(type: height) { result, date in
                print("HEIGHT: \(result.doubleValue(for: .meterUnit(with: .centi)))")
            }
            
            self.healthStore.read(type: bodyFat) { result, date in
                print("KG: \(result.doubleValue(for: .gramUnit(with: .kilo)))")
            }
            
            self.healthStore.read(type: bodyMassIndex) { result, date in
                print("BODY MASS INDEX: \(result.doubleValue(for: .gramUnit(with: .kilo)))")
            }
  
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

class HealthStore {

    private let healthStore = HKHealthStore()

    func authorizeHealthKit(completion: @escaping ((_ success: Bool, _ error: Error?) -> Void)) {

//        if !HKHealthStore.isHealthDataAvailable() {
//            return
//        }
        
        

        let readDataTypes: Set<HKSampleType> = [
            HKSampleType.quantityType(forIdentifier: .bodyMass)!,
            HKSampleType.quantityType(forIdentifier: .height)!,
            HKSampleType.quantityType(forIdentifier: .bodyFatPercentage)!,
            HKSampleType.quantityType(forIdentifier: .bodyMassIndex)!,
        ]

        healthStore.requestAuthorization(toShare: nil, read: readDataTypes) { (success, error) in
            completion(success, error)
        }

    }


    //returns the weight entry in Kilos or nil if no data
    func read(type: HKSampleType, completion: @escaping ((_ result: HKQuantity, _ date: Date) -> Void)) {

        let query = HKSampleQuery(sampleType: type, predicate: nil, limit: 1, sortDescriptors: nil) { (query, results, error) in
            if let result = results?.first as? HKQuantitySample {
                let value = result.quantity
//                doubleValue(for: HKUnit.gramUnit(with: .kilo))
                completion(value, result.endDate)
                return
            }

            //no data
//            completion(nil, nil)
        }
        healthStore.execute(query)
    }

}


```
