import SwiftUI

struct Expense: Identifiable, Codable {
    var id = UUID()
    var name: String
    var amount: Double
    var category: String
}

class ExpenseViewModel: ObservableObject {
    @Published var expenses: [Expense] {
        didSet {
            if let encoded = try? JSONEncoder().encode(expenses) {
                UserDefaults.standard.set(encoded, forKey: "Expenses")
            }
        }
    }
    
    init() {
        if let savedExpenses = UserDefaults.standard.data(forKey: "Expenses"),
           let decodedExpenses = try? JSONDecoder().decode([Expense].self, from: savedExpenses) {
            expenses = decodedExpenses
        } else {
            expenses = []
        }
    }
    
    func addExpense(name: String, amount: Double, category: String) {
        let newExpense = Expense(name: name, amount: amount, category: category)
        expenses.append(newExpense)
    }
    
    func removeExpense(at offsets: IndexSet) {
        expenses.remove(atOffsets: offsets)
    }
    
    var totalAmount: Double {
        expenses.reduce(0) { $0 + $1.amount }
    }
}

struct ContentView: View {
    @StateObject var viewModel = ExpenseViewModel()
    @State private var name = ""
    @State private var amount = ""
    @State private var category = ""
    @State private var showingAddExpense = false
    @State private var showingSummary = false
    
    var body: some View {
        NavigationView {
            VStack {
                HStack {
                    Button(action: { showingSummary = true }) {
                        Text("Total: $\(viewModel.totalAmount, specifier: "%.2f")")
                            .font(.headline)
                            .padding()
                    }
                    .sheet(isPresented: $showingSummary) {
                        SummaryView(expenses: viewModel.expenses)
                    }
                    Spacer()
                }
                
                List {
                    ForEach(viewModel.expenses) { expense in
                        HStack {
                            VStack(alignment: .leading) {
                                Text(expense.name).font(.headline)
                                Text(expense.category).font(.subheadline).foregroundColor(.gray)
                            }
                            Spacer()
                            Text("$\(expense.amount, specifier: "%.2f")")
                        }
                    }
                    .onDelete(perform: viewModel.removeExpense)
                }
                .navigationTitle("Expense Tracker")
                .toolbar {
                    Button(action: { showingAddExpense = true }) {
                        Image(systemName: "plus")
                    }
                }
                .sheet(isPresented: $showingAddExpense) {
                    AddExpenseView(viewModel: viewModel)
                }
            }
        }
    }
}

struct SummaryView: View {
    let expenses: [Expense]
    
    var body: some View {
        NavigationView {
            List {
                ForEach(expenses) { expense in
                    HStack {
                        Text(expense.name)
                        Spacer()
                        Text("$\(expense.amount, specifier: "%.2f")")
                    }
                }
            }
            .navigationTitle("Expense Summary")
        }
    }
}

struct AddExpenseView: View {
    @Environment(\.presentationMode) var presentationMode
    @ObservedObject var viewModel: ExpenseViewModel
    @State private var name = ""
    @State private var amount = ""
    @State private var category = ""
    
    var body: some View {
        NavigationView {
            Form {
                TextField("Expense Name", text: $name)
                TextField("Amount", text: $amount)
                    .keyboardType(.decimalPad)
                TextField("Category", text: $category)
            }
            .navigationTitle("Add Expense")
            .toolbar {
                Button("Save") {
                    if let amountValue = Double(amount) {
                        viewModel.addExpense(name: name, amount: amountValue, category: category)
                        presentationMode.wrappedValue.dismiss()
                    }
                }
            }
        }
    }
}

@main
struct ExpenseTrackerApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

#if DEBUG
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
#endif
