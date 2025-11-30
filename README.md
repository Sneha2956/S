def main():
    n = int(input("Enter number of processes: "))

    processes = []
    for i in range(1, n + 1):
        at = int(input(f"Arrival time of process {i}: "))
        bt = int(input(f"Burst time of process {i}: "))
        processes.append({'pid': i, 'arrival': at, 'burst': bt})

    processes.sort(key=lambda x: x['arrival'])

    current_time = 0
    total_wait = 0
    total_turnaround = 0
    total_idle = 0
    order = []

    for p in processes:
        if current_time < p['arrival']:
            idle = p['arrival'] - current_time
            total_idle += idle

            order.append("IDLE")
            current_time = p['arrival']

        p['start'] = current_time
        p['finish'] = current_time + p['burst']
        p['wait'] = p['start'] - p['arrival']
        p['turnaround'] = p['finish'] - p['arrival']

        total_wait += p['wait']
        total_turnaround += p['turnaround']
        current_time = p['finish']
        order.append(f"P{p['pid']}")


    print("\nGantt Chart:")
    print(" --> ".join(order))

    print("\nProcess\tArrival\tBurst\tWait\tTurnaround")
    for p in sorted(processes, key=lambda x: x['pid']):
        print(f"P{p['pid']}\t{p['arrival']}\t{p['burst']}\t{p['wait']}\t{p['turnaround']}")


    print(f"\nTotal CPU idle time: {total_idle} units")


if __name__ == "__main__":
    main()
#fcfs without
def main():
    n = int(input("Enter number of processes: "))

    processes = []
    for i in range(1, n + 1):
        bt = int(input(f"Burst time of process {i}: "))
        processes.append({'pid': i, 'burst': bt})

    current_time = 0
    order = []

    print("\nProcess\tBurst\tWait\tTurnaround")

    for p in processes:
        p['wait'] = current_time
        p['turnaround'] = current_time + p['burst']
        current_time += p['burst']
        order.append(f"P{p['pid']}")

        print(f"P{p['pid']}\t{p['burst']}\t{p['wait']}\t{p['turnaround']}")

    print("\nGantt Chart:")
    print(" --> ".join(order))


if __name__ == "__main__":
    main()
    
#sjf-non-with at
def main():
    n = int(input("Enter number of processes: "))

    processes = []
    for i in range(1, n + 1):
        at = int(input(f"Arrival time of process {i}: "))
        bt = int(input(f"Burst time of process {i}: "))
        processes.append({'pid': i, 'arrival': at, 'burst': bt, 'done': False})

    current_time = 0
    total_wait = 0
    total_turnaround = 0
    total_idle = 0
    completed = 0
    order = []

    while completed < n:
      
        available = [p for p in processes if p['arrival'] <= current_time and not p['done']]

        if not available:
            next_arrival = min(p['arrival'] for p in processes if not p['done'])
            idle = next_arrival - current_time
            total_idle += idle
            current_time = next_arrival
            order.append("IDLE")
            continue

        current = min(available, key=lambda x: x['burst'])

        current['start'] = current_time
        current['finish'] = current_time + current['burst']
        current['wait'] = current['start'] - current['arrival']
        current['turnaround'] = current['finish'] - current['arrival']
        current['done'] = True

        total_wait += current['wait']
        total_turnaround += current['turnaround']
        current_time = current['finish']
        completed += 1
        order.append(f"P{current['pid']}")

    print("\nGantt Chart:")
    print(" --> ".join(order))

    print("\nProcess\tArrival\tBurst\tWait\tTurnaround")
    for p in sorted(processes, key=lambda x: x['pid']):
        print(f"P{p['pid']}\t{p['arrival']}\t{p['burst']}\t{p['wait']}\t{p['turnaround']}")

    print(f"\nTotal CPU idle time: {total_idle} units")


if __name__ == "__main__":
    main()
    
#sjf non without at
def main():
    n = int(input("Enter number of processes: "))

    processes = []
    for i in range(1, n + 1):
        bt = int(input(f"Burst time of process {i}: "))
        processes.append({'pid': i, 'burst': bt, 'done': False})

    current_time = 0
    order = []
    completed = 0

    print("\nProcess\tBurst\tWait\tTurnaround")

    while completed < n:
        # Pick smallest burst time among NOT done processes
        current = min([p for p in processes if not p['done']], key=lambda x: x['burst'])

        current['start'] = current_time
        current['finish'] = current_time + current['burst']
        current['wait'] = current['start']
        current['turnaround'] = current['finish']

        current_time = current['finish']
        current['done'] = True
        completed += 1
        order.append(f"P{current['pid']}")

    # Print table
    for p in sorted(processes, key=lambda x: x['pid']):
        print(f"P{p['pid']}\t{p['burst']}\t{p['wait']}\t{p['turnaround']}")

    print("\nGantt Chart:")
    print(" --> ".join(order))


if __name__ == "__main__":
    main()
#sjf preemp without at
def main():
    n = int(input("Enter number of processes: "))

    processes = []
    for i in range(1, n + 1):
        bt = int(input(f"Burst time of process {i}: "))
        processes.append({
            'pid': i,
            'burst': bt,
            'remaining': bt,
            'start': -1,
            'finish': 0,
            'wait': 0,
            'turnaround': 0
        })

    current_time = 0
    completed = 0
    order = []

    # Run until all processes finish
    while completed < n:
        # Pick process with the smallest remaining time
        available = [p for p in processes if p['remaining'] > 0]
        current = min(available, key=lambda x: x['remaining'])

        # Mark start time only once
        if current['start'] == -1:
            current['start'] = current_time

        # Execute for 1 time unit
        order.append(f"P{current['pid']}")
        current['remaining'] -= 1
        current_time += 1

        # If finished
        if current['remaining'] == 0:
            current['finish'] = current_time
            current['turnaround'] = current['finish']
            current['wait'] = current['turnaround'] - current['burst']
            completed += 1

    # Print results
    print("\nProcess\tBurst\tWait\tTurnaround")
    for p in sorted(processes, key=lambda x: x['pid']):
        print(f"P{p['pid']}\t{p['burst']}\t{p['wait']}\t{p['turnaround']}")

    # Print Gantt chart (merged)
    merged = []
    for i in range(len(order)):
        if i == 0 or order[i] != order[i-1]:
            merged.append(order[i])

    print("\nGantt Chart:")
    print(" --> ".join(merged))


if __name__ == "__main__":
    main()

#sjf preemp with at
def main():
    n = int(input("Enter number of processes: "))

    processes = []
    for i in range(1, n + 1):
        at = int(input(f"Arrival time of process {i}: "))
        bt = int(input(f"Burst time of process {i}: "))
        processes.append({
            'pid': i,
            'arrival': at,
            'burst': bt,
            'remaining': bt,
            'start': -1,
            'finish': 0,
            'wait': 0,
            'turnaround': 0
        })

    current_time = 0
    completed = 0
    order = []
    total_idle = 0

    while completed < n:
        # Available processes at current time
        available = [p for p in processes if p['arrival'] <= current_time and p['remaining'] > 0]

        if not available:
            # CPU idle until next process arrives
            next_arrival = min(p['arrival'] for p in processes if p['remaining'] > 0)
            total_idle += next_arrival - current_time
            current_time = next_arrival
            order.append("IDLE")
            continue

        # Pick process with smallest remaining time
        current = min(available, key=lambda x: x['remaining'])

        # Mark start time if first execution
        if current['start'] == -1:
            current['start'] = current_time

        # Execute for 1 time unit
        order.append(f"P{current['pid']}")
        current['remaining'] -= 1
        current_time += 1

        # If process finished
        if current['remaining'] == 0:
            current['finish'] = current_time
            current['turnaround'] = current['finish'] - current['arrival']
            current['wait'] = current['turnaround'] - current['burst']
            completed += 1

    # Print process table
    print("\nProcess\tArrival\tBurst\tWait\tTurnaround")
    for p in sorted(processes, key=lambda x: x['pid']):
        print(f"P{p['pid']}\t{p['arrival']}\t{p['burst']}\t{p['wait']}\t{p['turnaround']}")

    # Merge repeated entries for Gantt chart
    merged = []
    for i in range(len(order)):
        if i == 0 or order[i] != order[i-1]:
            merged.append(order[i])

    print("\nGantt Chart:")
    print(" --> ".join(merged))
    print(f"\nTotal CPU idle time: {total_idle} units")


if __name__ == "__main__":
    main()


